---
title: activeElement
description: 현재 포커스된 DOM 요소를 추적하고 액세스합니다
category: Elements
---

<script>
import Demo from '$lib/components/demos/active-element.svelte';
</script>

`activeElement`는 애플리케이션에서 현재 포커스된 DOM 요소에 대한 반응형 액세스를 제공하며,
`document.activeElement`와 유사하지만 반응형 업데이트가 지원됩니다.

- DOM 포커스 변경과 동기적으로 업데이트
- 포커스된 요소가 없을 때 `null` 반환
- SSR(서버 사이드 렌더링)과 안전하게 사용 가능
- 수동 포커스 추적의 경량 대안
- Shadow DOM 경계를 검색하여 실제 활성 요소 찾기

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { activeElement } from "runed";
</script>

<p>
	Currently active element:
	{activeElement.current?.localName ?? "No active element found"}
</p>
```

## 사용자 정의 문서

커스텀 문서나 shadow root 내에서 포커스 추적 범위를 지정하려면 `ActiveElement` 옵션에
`DocumentOrShadowRoot`를 전달할 수 있습니다:

```svelte
<script lang="ts">
	import { ActiveElement } from "runed";

	const activeElement = new ActiveElement({
		document: shadowRoot
	});
</script>
```

## 타입 정의

```ts
interface ActiveElement {
	readonly current: Element | null;
}
```
