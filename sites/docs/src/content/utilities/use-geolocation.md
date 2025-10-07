---
title: useGeolocation
description: 브라우저의 Geolocation API에 대한 반응형 접근을 제공합니다.
category: Sensors
---

<script>
import Demo from '$lib/components/demos/use-geolocation.svelte';
</script>

`useGeolocation`은 브라우저의 [Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)에 대한 반응형 래퍼입니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { useGeolocation } from "runed";

	const location = useGeolocation();
</script>

<pre>좌표: {JSON.stringify(location.position.coords, null, 2)}</pre>
<pre>위치 확인 시간: {location.position.timestamp}</pre>
<pre>에러: {JSON.stringify(location.error, null, 2)}</pre>
<pre>지원 여부: {location.isSupported}</pre>
<button onclick={location.pause} disabled={location.isPaused}>일시정지</button>
<button onclick={location.resume} disabled={!location.isPaused}>재개</button>
```

## 타입 정의

```ts
type UseGeolocationOptions = Partial<PositionOptions> & {
	/**
	 * 생성 즉시 감시자를 시작할지 여부입니다.
	 * `false`로 설정하면 `resume()`이 호출될 때만 위치 추적을 시작합니다.
	 *
	 * @defaultValue true
	 */
	immediate?: boolean;
};

type UseGeolocationReturn = {
	readonly isSupported: boolean;
	readonly position: Omit<GeolocationPosition, "toJSON">;
	readonly error: GeolocationPositionError | null;
	readonly isPaused: boolean;
	pause: () => void;
	resume: () => void;
};
```
