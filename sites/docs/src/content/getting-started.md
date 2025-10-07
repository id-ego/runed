---
title: 시작하기
description: 프로젝트에 Runed를 설치하고 사용하는 방법을 알아보세요.
category: Anchor
---

## 설치

선호하는 패키지 매니저를 사용하여 Runed를 설치하세요:

```bash
npm install runed
```

## 사용법

필요한 유틸리티 중 하나를 `.svelte` 또는 `.svelte.js|ts` 파일로 가져와서 사용을 시작하세요:

```svelte title="component.svelte"
<script lang="ts">
	import { activeElement } from "runed";

	let inputElement = $state<HTMLInputElement | undefined>();
</script>

<input bind:this={inputElement} />

{#if activeElement.current === inputElement}
	The input element is active!
{/if}
```

또는

```ts title="some-module.svelte.ts"
import { activeElement } from "runed";

function logActiveElement() {
	$effect(() => {
		console.log("Active element is ", activeElement.current);
	});
}

logActiveElement();
```
