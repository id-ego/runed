---
title: IsDocumentVisible
description: Page Visibility API를 사용하여 현재 문서의 표시 여부를 추적합니다.
category: Sensors
---

<script>
import Demo from '$lib/components/demos/is-document-visible.svelte';
</script>

`IsDocumentVisible`은 현재 문서의 가시성 상태를 반영하는 반응형 boolean을 제공합니다.
`visibilitychange` 이벤트를 수신하고 자동으로 업데이트됩니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { IsDocumentVisible } from "runed";

	const visible = new IsDocumentVisible();
</script>

<p>Document visible: {visible.current ? "Yes" : "No"}</p>
```

## 타입 정의

```ts
type IsDocumentVisibleOptions = {
	window?: Window;
	document?: Document;
};

class IsDocumentVisible {
	constructor(options?: IsDocumentVisibleOptions);
	readonly current: boolean; // 문서가 보일 때 true, 숨겨질 때 false
}
```

## 참고사항

- `document.hidden` 및 `visibilitychange`를 통해 Page Visibility API를 사용합니다.
- 브라우저가 아닌 컨텍스트에서는 `current`가 기본적으로 `false`입니다.
