---
title: onCleanup
description:
  현재 이펙트 컨텍스트가 해제될 때 호출될 정리 함수를 등록합니다
category: Utilities
---

`onCleanup`을 사용하면 현재 이펙트 컨텍스트가 해제될 때 호출될 정리 함수를 등록할 수 있습니다. 이는 컴포넌트가 파괴되거나 루트 이펙트가 해제될 때 발생합니다.

이 유틸리티는 다음과 같은 코드의 단축형입니다:

```ts
$effect(() => {
	return () => {
		// cleanup
	};
});
```

## 사용법

```svelte
<script lang="ts">
	import { onCleanup } from "runed";

	// can act as a replacement for `onDestroy`
	onCleanup(() => {
		console.log("Component is being cleaned up!");
	});

	// can be used within a root effect to call a cleanup function when the root effect is disposed
	$effect.root(() => {
		onCleanup(() => {
			console.log("Root effect is being cleaned up!");
		});
	});
</script>
```

## 타입 정의

```ts
function onCleanup(cb: () => void): void;
```
