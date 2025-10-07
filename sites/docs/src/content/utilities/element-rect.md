---
title: ElementRect
description: 요소의 크기와 위치를 반응적으로 추적합니다
category: Elements
---

<script>
	import Demo from '$lib/components/demos/element-rect.svelte';
</script>

`ElementRect`는 요소의 크기와 위치 정보에 대한 반응형 접근을 제공하며, 요소의 크기나 위치가 변경될 때 자동으로 업데이트됩니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { ElementRect } from "runed";

	let el = $state<HTMLElement>();
	const rect = new ElementRect(() => el);
</script>

<textarea bind:this={el}></textarea>

<p>Width: {rect.width} Height: {rect.height}</p>
<!-- alternatively -->
<pre>{JSON.stringify(rect.current, null, 2)}</pre>
```

## 타입 정의

```ts
type Rect = Omit<DOMRect, "toJSON">;

interface ElementRectOptions {
	initialRect?: DOMRect;
}

class ElementRect {
	constructor(node: MaybeGetter<HTMLElement | undefined | null>, options?: ElementRectOptions);
	readonly current: Rect;
	readonly width: number;
	readonly height: number;
	readonly top: number;
	readonly left: number;
	readonly right: number;
	readonly bottom: number;
	readonly x: number;
	readonly y: number;
}
```
