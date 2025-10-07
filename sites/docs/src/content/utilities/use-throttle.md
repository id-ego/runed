---
title: useThrottle
description: 함수의 실행을 조절하는 고차 함수입니다.
category: Utilities
---

<script>
 import Demo from '$lib/components/demos/use-throttle.svelte';
</script>

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { useThrottle, watch } from "runed";

	let search = $state("");
	let throttledSearch = $state("");
	let durationMs = $state(1000);

	const throttledUpdate = useThrottle(
		() => {
			throttledSearch = search;
		},
		() => durationMs
	);
</script>

<div>
	<input
		bind:value={
			() => search,
			(v) => {
				search = v;
				throttledUpdate();
			}
		} />
	<p>검색어: <b>{throttledSearch}</b></p>
</div>
```
