---
title: IsInViewport
description: 현재 뷰포트 내에서 요소가 보이는지 추적합니다.
category: Elements
---

<script>
import Demo from '$lib/components/demos/is-in-viewport.svelte';
</script>

`IsInViewport`은 [`useIntersectionObserver`](/docs/utilities/use-intersection-observer) 유틸리티를 사용하여 현재 뷰포트 내에서 요소가 보이는지 추적합니다.

요소 또는 요소를 반환하는 getter와 [`useIntersectionObserver`](/docs/utilities/use-intersection-observer) 유틸리티 옵션과 일치하는 선택적 `options` 객체를 받습니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { IsInViewport } from "runed";

	let targetNode = $state<HTMLElement>()!;
	const inViewport = new IsInViewport(() => targetNode);
</script>

<p bind:this={targetNode}>대상 노드</p>

<p>뷰포트 내 대상 노드: {inViewport.current}</p>
```

## 타입 정의

```ts
import { type UseIntersectionObserverOptions } from "runed";
export type IsInViewportOptions = UseIntersectionObserverOptions;

export declare class IsInViewport {
	constructor(node: MaybeGetter<HTMLElement | null | undefined>, options?: IsInViewportOptions);
	get current(): boolean;
}
```

<!-- Ensure the page can scroll so the target can be outside of the viewport -->
<div class="h-[500px]"></div>
