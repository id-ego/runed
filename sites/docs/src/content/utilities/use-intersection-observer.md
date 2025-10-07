---
title: useIntersectionObserver
description: 대상 요소의 교차 변경 사항을 감시합니다.
category: Elements
---

<script>
import Demo from '$lib/components/demos/use-intersection-observer.svelte';
import { Callout } from '@svecodocs/kit'
</script>

## 데모

<Demo />

## 사용법

요소에 대한 참조가 있으면 `useIntersectionObserver` 유틸리티를 사용하여 대상 요소의 교차 변경 사항을 감시할 수 있습니다.

```svelte
<script lang="ts">
	import { useIntersectionObserver } from "runed";

	let target = $state<HTMLElement | null>(null);
	let root = $state<HTMLElement | null>(null);

	let isIntersecting = $state(false);

	useIntersectionObserver(
		() => target,
		(entries) => {
			const entry = entries[0];
			if (!entry) return;
			isIntersecting = entry.isIntersecting;
		},
		{ root: () => root }
	);
</script>

<div bind:this={root}>
	<div bind:this={target}>
		{#if isIntersecting}
			<div>Target is intersecting</div>
		{:else}
			<div>Target is not intersecting</div>
		{/if}
	</div>
</div>
```

### 일시정지

`pause` 메서드를 호출하여 언제든지 intersection observer를 일시정지할 수 있습니다.

```ts
const observer = useIntersectionObserver(/* ... */);

observer.pause();
```

### 재개

`resume` 메서드를 호출하여 언제든지 intersection observer를 재개할 수 있습니다.

```ts
const observer = useIntersectionObserver(/* ... */);

observer.resume();
```

### 중지

`stop` 메서드를 호출하여 언제든지 intersection observer를 중지할 수 있습니다.

```ts
const observer = useIntersectionObserver(/* ... */);

observer.stop();
```

### isActive

`isActive` 속성을 확인하여 intersection observer가 활성 상태인지 확인할 수 있습니다.

<Callout type="warning">

이 속성은 getter이므로 구조 분해할 수 없습니다. observer에서 직접 접근해야 합니다.

</Callout>

```ts
const observer = useIntersectionObserver(/* ... */);

if (observer.isActive) {
	// do something
}
```
