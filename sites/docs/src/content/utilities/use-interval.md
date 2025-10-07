---
title: useInterval
description: 일시 정지 및 재개 제어 기능을 제공하는 setInterval 래퍼입니다.
category: Utilities
---

<script>
	import Demo from '$lib/components/demos/use-interval.svelte';
</script>

`useInterval`은 실행을 일시 정지하고 재개할 수 있는 제어 기능과 함께 `setInterval`을 반응형으로 감싸는 유틸리티 함수입니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { useInterval } from "runed";

	let count = $state(0);
	let intervalDelay = $state(1000);

	const interval = useInterval(
		() => count++,
		() => intervalDelay
	);
</script>

<p>Count: {count}</p>
<p>Interval delay: {intervalDelay}ms</p>
<p>Status: {isActive ? "Running" : "Paused"}</p>

<input type="number" bind:value={intervalDelay} min="100" step="100" />

<button onclick={interval.pause} disabled={!interval.isActive}>Pause</button>
<button onclick={interval.resume} disabled={interval.isActive}>Resume</button>
```

## 옵션

`useInterval` 함수는 다음 옵션들을 포함하는 선택적 세 번째 매개변수를 받습니다:

- `immediate` (기본값: `true`) - 인터벌을 즉시 시작할지 여부
- `immediateCallback` (기본값: `false`) - 재개할 때 콜백을 즉시 실행할지 여부

```svelte
<script lang="ts">
	import { useInterval } from "runed";

	let count = $state(0);

	const interval = useInterval(() => count++, 1000, {
		immediate: false, // Don't start automatically
		immediateCallback: true // Execute callback immediately on resume
	});
</script>
```

## 반응형 인터벌

인터벌 지연 시간은 반응형일 수 있으며, 타이머는 값이 변경될 때 새로운 인터벌로 자동으로 재시작됩니다:

```svelte
<script lang="ts">
	import { useInterval } from "runed";

	let count = $state(0);
	let speed = $state(1000);

	// The interval will update reactively when speed changes
	const interval = useInterval(
		() => count++,
		() => speed
	);
</script>
```
