---
title: TextareaAutosize
description: 콘텐츠에 따라 textarea의 높이를 자동으로 조정합니다.
category: Elements
---

<script>
import Demo from '$lib/components/demos/textarea-autosize.svelte';
</script>

## 데모

<Demo />

## 개요

`TextareaAutosize`는 레이아웃 이동 없이 콘텐츠에 따라 `<textarea>` 요소가 자동으로 커지거나 작아지도록 만드는 유틸리티입니다. 다음과 같은 기능을 제공합니다:

- 정확한 측정을 위해 실제 textarea를 화면 밖에 미러링합니다
- 콘텐츠, 너비 또는 요소가 변경될 때 크기를 동기화합니다
- 최대 높이(구성된 경우)까지 오버플로를 방지합니다
- 반응형 상태와 정적 값을 모두 지원합니다

## 사용법

```svelte
<script lang="ts">
	import { TextareaAutosize } from "runed";

	let el = $state<HTMLTextAreaElement>(null!);
	let value = $state("");

	new TextareaAutosize({
		element: () => el,
		input: () => value
	});
</script>

<textarea bind:this={el} bind:value></textarea>
```

입력하면 textarea가 콘텐츠에 맞게 세로로 자동으로 크기가 조정됩니다.

## 옵션

다음 옵션을 통해 동작을 사용자 정의할 수 있습니다:

| 옵션        | 타입                               | 설명                                                                                                |
| ----------- | ---------------------------------- | -------------------------------------------------------------------------------------------------- |
| `element`   | `Getter<HTMLElement \| undefined>` | 대상 textarea (필수).                                                                              |
| `input`     | `Getter<string>`                   | 반응형 입력 값 (필수).                                                                             |
| `onResize`  | `() => void`                       | 높이가 업데이트될 때마다 호출됩니다.                                                                |
| `styleProp` | `"height"` \| `"minHeight"`        | 크기를 제어하는 CSS 속성. `"height"`는 양방향으로 크기 조정. `"minHeight"`는 증가만. 기본값: `"height"`. |
| `maxHeight` | `number`                           | 스크롤이 나타나기 전 최대 높이(픽셀). 기본값: 무제한.                                               |

## 동작

내부적으로 `TextareaAutosize`는:

- 보이지 않는 화면 밖 `<textarea>` 복제본을 생성합니다
- 실제 textarea에서 계산된 스타일을 복사합니다
- 복제본의 스크롤 높이를 측정하여 필요한 높이를 결정합니다
- 실제 textarea에 높이(또는 `minHeight`)를 적용합니다
- 콘텐츠 변경, 요소 크기 조정 및 너비 변경 시 재계산합니다

## 예제

### 증가 전용 동작

```ts
new TextareaAutosize({
	element: () => el,
	input: () => value,
	styleProp: "minHeight"
});
```

이렇게 하면 textarea가 필요에 따라 확장되지만 현재 크기보다 작아지지는 않습니다.
