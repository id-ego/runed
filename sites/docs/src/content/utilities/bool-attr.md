---
title: boolAttr
description:
  존재 여부가 참을 나타내는 HTML 속성에 사용하기 위해 모든 값을 `""`(빈 문자열) 또는 `undefined`로 변환하는 유틸리티입니다.
category: Utilities
---

`boolAttr` 유틸리티는 존재 여부가 참을 나타내는 HTML 속성에 사용하기 위해 모든 값을 빈 문자열(`""`) 또는 `undefined`로 변환합니다. 이는 HTML에서 적절한 불린 속성을 생성하는 데 필수적이며, 속성의 존재 여부(값과 관계없이)가 참 상태를 나타냅니다.

## 문제

Svelte 속성에서 불린 값을 직접 사용하면 종종 문자열 값으로 렌더링됩니다:

```svelte
<!-- 다음과 같이 렌더링됨: <div data-active="true"> -->
<div data-active={true}>Content</div>

<!-- 다음과 같이 렌더링됨: <div data-active="false"> -->
<div data-active={false}>Content</div>
```

두 경우 모두 속성이 존재하게 되므로 CSS 선택자 및 속성 기반 스타일링에 문제가 발생합니다.

## 해결책

`boolAttr`은 적절한 불린 속성 동작을 보장합니다:

```svelte
<script lang="ts">
	import { boolAttr } from "runed";

	let isActive = $state(true);
	let isLoading = $state(false);
</script>

<!-- 다음과 같이 렌더링됨: <div data-active> -->
<div data-active={boolAttr(isActive)}>활성 콘텐츠</div>

<!-- 다음과 같이 렌더링됨: <div> (data-loading 속성 없음) -->
<div data-loading={boolAttr(isLoading)}>로딩 콘텐츠</div>
```

## 타입 정의

```ts
function boolAttr(value: unknown): "" | undefined;
```

### 매개변수

- **`value`** (`unknown`) - 불린 속성으로 변환할 모든 값

### 반환값

- **`""`** (빈 문자열) - `value`가 참일 때
- **`undefined`** - `value`가 거짓일 때
