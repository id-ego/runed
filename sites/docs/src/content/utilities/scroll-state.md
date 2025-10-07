---
title: ScrollState
description:
  프로그래매틱 스크롤 지원과 함께 스크롤 위치, 방향 및 가장자리 상태를 추적합니다.
category: Elements
---

<script>
import Demo from '$lib/components/demos/scroll-state.svelte';
</script>

## 데모

<Demo />

## 개요

`ScrollState`는 다음을 수행할 수 있는 반응형 유틸리티입니다:

- 스크롤 위치 추적 (`x` / `y`)
- 스크롤 방향 감지 (`left`, `right`, `top`, `bottom`)
- 사용자가 가장자리로 스크롤했는지 확인 (`arrived` 상태)
- 프로그래매틱 스크롤 수행 (`scrollTo`, `scrollToTop`, `scrollToBottom`)
- 스크롤 및 스크롤 종료 이벤트 수신
- flex, RTL 및 역방향 레이아웃 모드 지원

[VueUse의 `useScroll`](https://vueuse.org/useScroll)에서 영감을 받은 이 유틸리티는 Svelte용으로 제작되었으며 DOM 요소, `window` 또는 `document`와 함께 작동합니다.

## 사용법

```svelte
<script lang="ts">
	import { ScrollState } from "runed";

	let el = $state<HTMLElement>();

	const scroll = new ScrollState({
		element: () => el
	});
</script>

<div bind:this={el} style="overflow: auto; height: 200px;">
	<!-- 스크롤 가능한 콘텐츠 -->
</div>
```

이제 다음에 접근할 수 있습니다:

- `scroll.x` 및 `scroll.y` — 현재 스크롤 위치 (반응형, get/set)

- `scroll.directions` — 활성 스크롤 방향

- `scroll.arrived` — 스크롤이 각 가장자리에 도달했는지 여부

- `scroll.progress` — 사용자가 x/y 축에서 스크롤한 백분율

- `scroll.scrollTo(x, y)` — 프로그래매틱 스크롤

- `scroll.scrollToTop()` 및 `scroll.scrollToBottom()` — 헬퍼 함수

## 옵션

다음 옵션을 통해 `ScrollState`를 구성할 수 있습니다:

| 옵션                   | 타입                                                     | 설명                                                                   |
| ---------------------- | -------------------------------------------------------- | ---------------------------------------------------------------------- |
| `element`              | `MaybeGetter<HTMLElement \| Window \| Document \| null>` | 스크롤 컨테이너 (필수).                                                |
| `idle`                 | `MaybeGetter<number \| undefined>`                       | 스크롤 종료 후 디바운스 시간 (ms). 기본값: `200`.                     |
| `offset`               | `{ top?, bottom?, left?, right? }`                       | "arrived" 상태 감지를 위한 픽셀 임계값. 기본값: 모두 `0`.             |
| `onScroll`             | `(e: Event) => void`                                     | 스크롤 이벤트 콜백.                                                    |
| `onStop`               | `(e: Event) => void`                                     | 스크롤 중지 후 콜백.                                                   |
| `eventListenerOptions` | `AddEventListenerOptions`                                | 스크롤 리스너 옵션. 기본값: `{ passive: true, capture: false }`.       |
| `behavior`             | `ScrollBehavior`                                         | 스크롤 동작: `"auto"`, `"smooth"` 등. 기본값: `"auto"`.                |
| `onError`              | `(error: unknown) => void`                               | 선택적 에러 핸들러. 기본값: `console.error`.                           |

## 참고사항

- 스크롤 위치 (`x`, `y`)와 가장자리 도달 상태 (`arrived`) 모두 반응형 값입니다.

- `scroll.x`와 `scroll.y`를 프로그래매틱으로 변경할 수 있으며, 요소가 그에 따라 스크롤됩니다.

- 가장자리 상태를 계산할 때 레이아웃 방향 및 역방향 flex 설정이 고려됩니다.

- 디바운스된 `onStop`은 스크롤이 종료되고 사용자가 유휴 상태일 때 호출됩니다.
