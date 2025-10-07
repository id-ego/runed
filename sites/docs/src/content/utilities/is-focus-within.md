---
title: IsFocusWithin
description:
  지정된 컨테이너 요소 내에서 하위 요소에 포커스가 있는지 추적하는 유틸리티입니다.
category: Elements
---

<script>
import Demo from '$lib/components/demos/is-focus-within.svelte';
</script>

`IsFocusWithin`은 컨테이너 요소 내의 포커스 상태를 반응적으로 추적하며, 포커스가 변경되면 자동으로 업데이트됩니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { IsFocusWithin } from "runed";

	let formElement = $state<HTMLFormElement>();
	const focusWithinForm = new IsFocusWithin(() => formElement);
</script>

<p>Focus within form: {focusWithinForm.current}</p>
<form bind:this={formElement}>
	<input type="text" />
	<button type="submit">Submit</button>
</form>
```

## 타입 정의

```ts
class IsFocusWithin {
	constructor(node: MaybeGetter<HTMLElement | undefined | null>);
	readonly current: boolean;
}
```
