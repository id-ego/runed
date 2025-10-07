---
title: FiniteStateMachine
description: 강력한 타입의 유한 상태 머신을 정의합니다.
category: State
---

<script>
	import Demo from '$lib/components/demos/finite-state-machine.svelte';
</script>

## 데모

<Demo />

```ts
import { FiniteStateMachine } from "runed";
type MyStates = "disabled" | "idle" | "running";
type MyEvents = "toggleEnabled" | "start" | "stop";

const f = new FiniteStateMachine<MyStates, MyEvents>("disabled", {
	disabled: {
		toggleEnabled: "idle"
	},
	idle: {
		toggleEnabled: "disabled",
		start: "running"
	},
	running: {
		_enter: () => {
			f.debounce(2000, "stop");
		},
		stop: "idle",
		toggleEnabled: "disabled"
	}
});
```

## 사용법

유한 상태 머신(종종 "FSM"으로 약칭)은 여러 다른 상태 중 하나에 있을 수 있는 것을 추적하고 조작하는 데 유용합니다. 가능한 모든 _상태_와 한 상태에서 다른 상태로의 전환을 트리거할 수 있는 _이벤트_의 정의를 중앙 집중화합니다. 다음은 간단한 토글 스위치를 설명하는 상태 머신입니다:

```ts
import { FiniteStateMachine } from "runed";
type MyStates = "on" | "off";
type MyEvents = "toggle";

const f = new FiniteStateMachine<MyStates, MyEvents>("off", {
	off: {
		toggle: "on"
	},
	on: {
		toggle: "off"
	}
});
```

`FiniteStateMachine` 생성자의 첫 번째 인수는 초기 상태입니다. 두 번째 인수는 각 상태에 대한 하나의 키가 있는 객체입니다. 각 상태는 해당 상태에 대해 유효한 이벤트와 해당 이벤트가 이끌어야 하는 상태를 설명합니다.

위의 간단한 스위치 예제에는 두 가지 상태(`on` 및 `off`)가 있습니다. 어느 상태에서든 `toggle` 이벤트는 다른 상태로 이어집니다.

`f.send`를 사용하여 FSM에 이벤트를 보냅니다. `toggle` 이벤트를 보내려면 `f.send('toggle')`를 호출합니다.

### 액션

이벤트 핸들러에 대한 더 복잡한 로직을 원하거나 조건부로 다른 상태로 전환하려는 경우가 있을 수 있습니다. 문자열 대신 _액션_을 사용할 수 있습니다.

액션은 상태를 반환하는 함수입니다. 액션은 매개변수를 받을 수 있으며, 이러한 매개변수를 사용하여 다음에 올 상태를 동적으로 선택할 수 있습니다. 또한 아무것도 반환하지 않음으로써 상태 전환을 방지할 수도 있습니다.

```ts
import { FiniteStateMachine } from "runed";
type MyStates = "on" | "off" | "cooldown";
type MyEvents = "toggle";

const f = new FiniteStateMachine<MyStates, MyEvents>("off", {
	off: {
		toggle: () => {
			if (isTuesday) {
				// Switch can only turn on during Tuesdays
				return "on";
			}
			// All other days, nothing is returned and state is unchanged.
		}
	},
	on: {
		toggle: (heldMillis: number) => {
			// You can also dynamically return the next state!
			// Only turn off if switch is depressed for 3 seconds
			if (heldMillis > 3000) {
				return "off";
			}
		}
	}
});
```

### 라이프사이클 메서드

상태가 진입하거나 종료될 때마다 호출되는 특수 핸들러를 정의할 수 있습니다:

```ts
import { FiniteStateMachine } from "runed";
type MyStates = "on" | "off";
type MyEvents = "toggle";

const f = new FiniteStateMachine<MyStates, MyEvents>("off", {
	off: {
		toggle: "on",
		_enter: (meta) => {
			console.log("switch is off");
		},
		_exit: (meta) => {
			console.log("switch is no longer off");
		}
	},
	on: {
		toggle: "off",
		_enter: (meta) => {
			console.log("switch is on");
		},
		_exit: (meta) => {
			console.log("switch is no longer on");
		}
	}
});
```

라이프사이클 메서드는 유용한 정보를 포함하는 메타데이터 객체와 함께 호출됩니다:

- `from`: 종료되는 이벤트의 이름
- `to`: 진입하는 이벤트의 이름
- `event`: 전환을 트리거한 이벤트의 이름
- `args`: (선택 사항) `f.send('theAction', additional, params, as, args)`로 액션을 호출할 때 추가 메타데이터를 전달할 수 있습니다

초기 상태에 대한 `_enter` 핸들러는 FSM 생성 시 호출됩니다. `from` 및 `event` 필드가 모두 `null`로 설정된 상태로 호출됩니다.

### 와일드카드 핸들러

대체용으로 사용되는 특수 상태가 하나 있습니다: `*`. 대체 상태가 있고 현재 상태에서 처리되지 않는 이벤트를 `send()`하려고 시도하면 이벤트를 폐기하기 전에 `*` 상태에서 해당 이벤트에 대한 핸들러를 찾으려고 시도합니다:

```ts
import { FiniteStateMachine } from "runed";
type MyStates = "on" | "off";
type MyEvents = "toggle" | "emergency";

const f = new FiniteStateMachine<MyStates, MyEvents>("off", {
	off: {
		toggle: "on"
	},
	on: {
		toggle: "off"
	},
	"*": {
		emergency: "off"
	}
});

// will always result in the switch turning off.
f.send("emergency");
```

### 디바운싱

일정 시간이 경과한 후 다른 상태로 전환하려는 경우가 자주 있습니다. 이를 위해 `debounce` 메서드를 사용합니다:

```ts
f.send("toggle"); // turn on immediately
f.debounce(5000, "toggle"); // turn off in 5000 milliseconds
```

동일한 이벤트로 debounce를 다시 호출하면 기존 타이머를 취소하고 카운트다운을 다시 시작합니다:

```ts
// schedule a toggle in five seconds
f.debounce(5000, "toggle");
// ... less than 5000ms elapses ...
f.debounce(5000, "toggle");
// The second call cancels the original timer, and starts a new one
```

액션과 라이프사이클 메서드 모두에서 `debounce`를 사용할 수도 있습니다. 다음 두 예제 모두에서 전등 스위치는 켜진 후 5초 후에 자동으로 꺼집니다:

```ts
const f = new FiniteStateMachine<MyStates, MyEvents>("off", {
	off: {
		toggle: () => {
			f.debounce(5000, "toggle");
			return "on";
		}
	},
	on: {
		toggle: "off"
	}
});
```

```ts
const f = new FiniteStateMachine<MyStates, MyEvents>("off", {
	off: {
		toggle: "on"
	},
	on: {
		toggle: "off",
		_enter: () => {
			f.debounce(5000, "toggle");
		}
	}
});
```

## 참고사항

`FiniteStateMachine`은 [kenkunz/svelte-fsm](https://github.com/kenkunz/svelte-fsm)의 애정 어린 재작성입니다.

FSM은 다양한 종류의 시스템과 상호작용 패턴을 표현하는 데 이상적입니다. `FiniteStateMachine`은 의도적으로 미니멀한 구현입니다. 더 강력한 FSM 라이브러리를 찾고 있다면 [statelyai/xstate](https://github.com/statelyai/xstate)가 더 많은 기능과 더 가파른 학습 곡선을 가진 훌륭한 라이브러리입니다.
