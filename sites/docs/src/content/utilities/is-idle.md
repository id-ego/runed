---
title: IsIdle
description: 사용자가 유휴 상태인지와 마지막 활동 시간을 추적합니다.
category: Sensors
---

<script>
import Demo from '$lib/components/demos/is-idle.svelte';
</script>

`IsIdle`은 사용자 활동을 추적하고 구성 가능한 타임아웃을 기반으로 유휴 상태인지 판단합니다. 마우스 움직임, 키보드 입력 및 터치 이벤트를 모니터링하여 사용자 상호작용을 감지합니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { AnimationFrames, IsIdle } from "runed";

	const idle = new IsIdle({ timeout: 1000 });
</script>

<p>Idle: {idle.current}</p>
<p>
	Last active: {new Date(idle.lastActive).toLocaleTimeString()}
</p>
```

## 타입 정의

```ts
interface IsIdleOptions {
	/**
	 * The events that should set the idle state to `true`
	 *
	 * @default ['mousemove', 'mousedown', 'resize', 'keydown', 'touchstart', 'wheel']
	 */
	events?: MaybeGetter<(keyof WindowEventMap)[]>;
	/**
	 * The timeout in milliseconds before the idle state is set to `true`. Defaults to 60 seconds.
	 *
	 * @default 60000
	 */
	timeout?: MaybeGetter<number>;
	/**
	 * Detect document visibility changes
	 *
	 * @default false
	 */
	detectVisibilityChanges?: MaybeGetter<boolean>;
	/**
	 * The initial state of the idle property
	 *
	 * @default false
	 */
	initialState?: boolean;
}

class IsIdle {
	constructor(options?: IsIdleOptions);
	readonly current: boolean;
	readonly lastActive: number;
}
```
