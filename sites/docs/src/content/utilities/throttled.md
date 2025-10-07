---
title: Throttled
description: 스로틀된 상태를 반환하는 `useThrottle`의 래퍼입니다.
category: State
---

<script>
import Demo from '$lib/components/demos/throttled.svelte';
</script>

## 데모

<Demo />

## 사용법

스로틀된 상태를 반환하는 [`useThrottle`](https://runed.dev/docs/utilities/use-throttle)의 간단한 래퍼입니다.

```svelte
<script lang="ts">
	import { Throttled } from "runed";

	let search = $state("");
	const throttled = new Throttled(() => search, 500);
</script>

<div>
	<input bind:value={search} />
	<p>You searched for: <b>{throttled.current}</b></p>
</div>
```

대기 중인 업데이트를 취소하거나 즉시 새 값을 설정할 수 있습니다. 즉시 설정하면 대기 중인 모든 업데이트도 취소됩니다.

```ts
let count = $state(0);
const throttled = new Throttled(() => count, 500);
count = 1;
throttled.cancel();
// after a while...
console.log(throttled.current); // Still 0!

count = 2;
console.log(throttled.current); // Still 0!
throttled.setImmediately(count);
console.log(throttled.current); // 2
```
