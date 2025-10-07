---
title: useEventListener
description: 자동으로 정리되는 이벤트 리스너를 연결하는 함수입니다.
category: Browser
---

<script>
import Demo from '$lib/components/demos/use-event-listener.svelte';
</script>

## 데모

<Demo />

## 사용법

`useEventListener` 함수는 직접 제어하지 않는 요소에 이벤트 리스너를 연결할 때 특히 유용합니다. 예를 들어, `<svelte:body />`를 사용할 수 없는 상황에서 document body나 window의 이벤트를 수신해야 하거나, 부모 컴포넌트로부터 요소 참조를 받는 경우입니다.

### 예제: 문서의 클릭 추적

```ts
// ClickLogger.ts
import { useEventListener } from "runed";

export class ClickLogger {
	#clicks = $state(0);

	constructor() {
		useEventListener(
			() => document.body,
			"click",
			() => this.#clicks++
		);
	}

	get clicks() {
		return this.#clicks;
	}
}
```

이 `ClickLogger` 클래스는 `useEventListener` 함수를 사용하여 document body의 클릭 수를 추적합니다. 클릭이 발생할 때마다 내부 카운터가 증가합니다.

### Svelte 컴포넌트 사용법

```svelte
<script lang="ts">
	import { ClickLogger } from "./ClickLogger.ts";

	const logger = new ClickLogger();
</script>

<p>
	You've clicked the document {logger.clicks}
	{logger.clicks === 1 ? "time" : "times"}
</p>
```

위 컴포넌트에서는 문서의 클릭을 모니터링하기 위해 `ClickLogger` 클래스의 인스턴스를 생성합니다. 표시되는 텍스트는 기록된 클릭 수에 따라 동적으로 업데이트됩니다.

### 주요 포인트

- **자동 정리:** 컴포넌트가 파괴되거나 요소 참조가 변경되면 이벤트 리스너가 자동으로 제거됩니다.
- **지연 초기화:** 대상 요소를 함수를 사용하여 정의할 수 있어 유연하고 동적인 동작이 가능합니다.
- **전역 리스너에 편리:** DOM 요소에 직접 이벤트 리스너를 연결하기 번거롭거나 비실용적인 시나리오에 이상적입니다.
