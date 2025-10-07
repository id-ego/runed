---
title: Interval
description:
  일시 정지 및 재개 제어 기능과 함께 매 간격마다 증가하는 반응형 카운터입니다.
category: Utilities
---

<script>
	import Demo from '$lib/components/demos/interval.svelte';
</script>

`Interval`은 VueUse의 `useInterval`과 유사하게 매 간격마다 증가하는 반응형 카운터를 제공하는 클래스입니다. 카운터를 일시 정지, 재개 및 재설정하는 제어 기능을 제공합니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { Interval } from "runed";

	const interval = new Interval(1000);
</script>

<p>카운터: {interval.counter}</p>
<p>상태: {interval.isActive ? "실행 중" : "일시 정지됨"}</p>

<button onclick={() => interval.pause()} disabled={!interval.isActive}>일시 정지</button>
<button onclick={() => interval.resume()} disabled={interval.isActive}>재개</button>
<button onclick={() => interval.reset()}>재설정</button>
```

## 콜백 사용

매 간격마다 실행될 콜백을 제공할 수 있습니다:

```svelte
<script lang="ts">
	import { Interval } from "runed";

	let message = $state("");

	const interval = new Interval(1000, {
		callback: (count) => {
			message = `틱 ${count}`;
		}
	});
</script>

<p>카운터: {interval.counter}</p><p>메시지: {message}</p>
```

## 옵션

`Interval` 생성자는 다음 옵션을 포함하는 선택적 두 번째 매개변수를 받습니다:

- `immediate` (기본값: `true`) - 간격을 즉시 시작할지 여부
- `callback` - 현재 카운터 값과 함께 매 간격마다 호출될 함수

```svelte
<script lang="ts">
	import { Interval } from "runed";

	const interval = new Interval(1000, {
		immediate: false, // 자동으로 시작하지 않음
		callback: (count) => console.log(`카운트: ${count}`)
	});
</script>
```

## 반응형 간격

간격 지연은 getter 함수를 사용하여 반응형으로 만들 수 있습니다:

```svelte
<script lang="ts">
	import { Interval } from "runed";

	let speed = $state(1000);

	// speed가 변경되면 간격이 반응적으로 업데이트됩니다
	const interval = new Interval(() => speed);
</script>

<input type="number" bind:value={speed} min="100" step="100" /><p>카운터: {interval.counter}</p>
```

## 속성

- `counter` - 현재 카운터 값 (읽기 전용)
- `isActive` - 간격이 현재 실행 중인지 여부 (읽기 전용)

## 메서드

- `pause()` - 간격 일시 정지
- `resume()` - 간격 재개
- `reset()` - 카운터를 0으로 재설정
