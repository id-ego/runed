---
title: Previous
description: 반응형 getter의 이전 값을 추적하고 액세스할 수 있는 유틸리티입니다.
category: State
---

<script>
import Demo from '$lib/components/demos/previous.svelte';
</script>

`Previous` 유틸리티는 getter 함수의 이전 값을 유지하는 반응형 래퍼를 생성합니다.
이는 상태 변경을 비교하거나 전환 효과를 구현해야 할 때 특히 유용합니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { Previous } from "runed";

	let count = $state(0);
	const previous = new Previous(() => count);
</script>

<div>
	<button onclick={() => count++}>Count: {count}</button>
	<pre>Previous: {`${previous.current}`}</pre>
</div>
```

## 타입 정의

```ts
class Previous<T> {
	constructor(getter: () => T);

	readonly current: T | undefined; // 이전 값
}
```
