---
title: Debounced
description: 디바운스된 상태를 반환하는 `useDebounce`의 래퍼입니다.
category: State
---

<script>
import Demo from '$lib/components/demos/debounced.svelte';
</script>

## 데모

<Demo />

## 사용법

디바운스된 상태를 반환하는 [`useDebounce`](https://runed.dev/docs/utilities/use-debounce)의 간단한 래퍼입니다.

```svelte
<script lang="ts">
	import { Debounced } from "runed";

	let search = $state("");
	const debounced = new Debounced(() => search, 500);
</script>

<div>
	<input bind:value={search} />
	<p>You searched for: <b>{debounced.current}</b></p>
</div>
```

대기 중인 업데이트를 취소하거나 즉시 실행하거나 새 값을 설정할 수 있습니다.
새 값을 즉시 설정하면 대기 중인 모든 업데이트도 취소됩니다.

```ts
let count = $state(0);
const debounced = new Debounced(() => count, 500);
count = 1;
debounced.cancel();
// 잠시 후...
console.log(debounced.current); // 여전히 0!

count = 2;
console.log(debounced.current); // 여전히 0!
debounced.setImmediately(count);
console.log(debounced.current); // 2

count = 3;
console.log(debounced.current); // 2
await debounced.updateImmediately();
console.log(debounced.current); // 3
```
