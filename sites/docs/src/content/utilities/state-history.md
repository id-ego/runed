---
title: StateHistory
description: 실행 취소/다시 실행 기능으로 상태 변경을 추적합니다
category: State
---

<script>
import Demo from '$lib/components/demos/state-history.svelte';
</script>

## 데모

<Demo />

## 사용법

`StateHistory`는 getter의 반환 값을 추적하여 각 변경 사항을 배열에 기록합니다. `undo` 및 `redo` 함수를 사용하려면 setter도 필요합니다.

<!-- prettier-ignore -->
```ts
import { StateHistory } from "runed";

let count = $state(0);
const history = new StateHistory(() => count, (c) => (count = c));
history.log[0]; // { snapshot: 0, timestamp: ... }
```

`log` 외에도 반환된 객체는 `undo`, `redo` 및 `clear` 기능을 포함합니다.

<!-- prettier-ignore -->
```svelte
<script lang="ts">
	import { StateHistory } from "runed";

	let count = $state(0);
	const history = new StateHistory(() => count, (c) => (count = c));
</script>

<p>{count}</p>

<button onclick={() => count++}>증가</button>
<button onclick={() => count--}>감소</button>

<button disabled={!history.canUndo} onclick={history.undo}>실행 취소</button>
<button disabled={!history.canRedo} onclick={history.redo}>다시 실행</button>
<button onclick={history.clear}>기록 지우기</button>
```

## 메서드

### `undo()`

기록 로그의 이전 값으로 상태를 되돌립니다. 현재 상태는 다시 실행 스택으로 이동됩니다.

<!-- prettier-ignore -->
```ts
let count = $state(0);
const history = new StateHistory(() => count, (c) => (count = c));

count = 1;
count = 2;

history.undo(); // count는 이제 1입니다
history.undo(); // count는 이제 0입니다
```

### `redo()`

이전에 실행 취소된 상태를 복원합니다. 다시 실행 스택에서 다음 상태를 기록 로그로 다시 이동합니다.

<!-- prettier-ignore -->
```ts
let count = $state(0);
const history = new StateHistory(() => count, (c) => (count = c));

count = 1;
count = 2;

history.undo(); // count는 이제 1입니다
history.redo();  // count는 이제 2입니다
```

### `clear()`

전체 기록 로그와 다시 실행 스택을 지워 상태 기록을 효과적으로 재설정합니다.

<!-- prettier-ignore -->
```ts
history.clear();
console.log(history.log); // []
console.log(history.canUndo); // false
console.log(history.canRedo); // false
```

## 속성

### `log`

상태 기록을 포함하는 `LogEvent<T>` 객체의 배열입니다. 각 이벤트에는 상태의 `snapshot`과 `timestamp`가 있습니다.

### `canUndo`

실행 취소가 가능한지 여부를 나타내는 파생된 불리언입니다 (로그에 둘 이상의 항목이 있을 때 true).

### `canRedo`

다시 실행이 가능한지 여부를 나타내는 파생된 불리언입니다 (다시 실행 스택이 비어 있지 않을 때 true).
