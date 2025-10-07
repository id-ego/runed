---
title: ElementSize
description: 요소의 크기를 반응적으로 추적합니다
category: Elements
---

<script>
	import Demo from '$lib/components/demos/element-size.svelte';
</script>

`ElementSize`는 요소의 너비와 높이에 대한 반응형 액세스를 제공하며, 요소의 크기가 변경되면 자동으로 업데이트됩니다.
`ElementRect`와 유사하지만 크기 측정에만 집중합니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { ElementSize } from "runed";

	let el = $state() as HTMLElement;
	const size = new ElementSize(() => el);
</script>

<textarea bind:this={el}></textarea>

<p>Width: {size.width} Height: {size.height}</p>
```

## 타입 정의

```ts
interface ElementSize {
	readonly width: number;
	readonly height: number;
}
```
