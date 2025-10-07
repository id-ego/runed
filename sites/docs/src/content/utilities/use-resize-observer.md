---
title: useResizeObserver
description: 요소의 크기 변화를 감지합니다
category: Elements
---

<script>
import Demo from '$lib/components/demos/use-resize-observer.svelte';
</script>

## 데모

<Demo />

## 사용법

요소에 대한 참조가 있으면 `useResizeObserver` 유틸리티를 사용하여 요소의 크기 변화를 감지할 수 있습니다.

```svelte
<script lang="ts">
	import { useResizeObserver } from "runed";

	let el = $state<HTMLElement | null>(null);
	let text = $state("");

	useResizeObserver(
		() => el,
		(entries) => {
			const entry = entries[0];
			if (!entry) return;

			const { width, height } = entry.contentRect;
			text = `width: ${width};\nheight: ${height};`;
		}
	);
</script>

<textarea bind:this={el} readonly value={text}></textarea>
```

`stop` 메서드를 호출하여 언제든지 resize observer를 중지할 수 있습니다.

```ts
const { stop } = useResizeObserver(/* ... */);
stop();
```
