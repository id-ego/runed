---
title: useMutationObserver
description: 요소의 변경 사항을 관찰합니다
category: Elements
---

<script>
import Demo from '$lib/components/demos/use-mutation-observer.svelte';
</script>

## 데모

<Demo />

## 사용법

요소에 대한 참조를 사용하여 `useMutationObserver` 훅으로 요소의 변경 사항을 관찰할 수 있습니다.

```svelte
<script lang="ts">
	import { useMutationObserver } from "runed";

	let el = $state<HTMLElement | null>(null);
	const messages = $state<string[]>([]);
	let className = $state("");
	let style = $state("");

	useMutationObserver(
		() => el,
		(mutations) => {
			const mutation = mutations[0];
			if (!mutation) return;

			messages.push(mutation.attributeName!);
		},
		{ attributes: true }
	);

	setTimeout(() => {
		className = "text-brand";
	}, 1000);

	setTimeout(() => {
		style = "font-style: italic;";
	}, 1500);
</script>

<div bind:this={el} class={className} {style}>
	{#each messages as text}
		<div>
			변경된 속성: {text}
		</div>
	{:else}
		<div>아직 변경 사항이 없습니다</div>
	{/each}
</div>
```

`stop` 메서드를 호출하여 언제든지 mutation observer를 중지할 수 있습니다.

```ts
const { stop } = useMutationObserver(/* ... */);
stop();
```
