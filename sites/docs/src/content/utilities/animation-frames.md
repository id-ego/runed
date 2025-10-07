---
title: AnimationFrames
description: FPS 제어 및 프레임 메트릭을 갖춘 requestAnimationFrame의 래퍼입니다
category: Animation
---

<script>
import Demo from '$lib/components/demos/animation-frames.svelte';
</script>

`AnimationFrames`는 브라우저의 [`requestAnimationFrame`](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame)에 대한 선언적 API를 제공하며, FPS 제한 기능과 프레임 메트릭을 제공하면서 자동으로 정리를 처리합니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { AnimationFrames } from "runed";
	import { Slider } from "../ui/slider"; // Check out shadcn-svelte!

	let frames = $state(0);
	let fpsLimit = $state(10);
	let delta = $state(0);
	const animation = new AnimationFrames(
		(args) => {
			frames++;
			delta = args.delta;
		},
		{ fpsLimit: () => fpsLimit }
	);

	const stats = $derived(
		`Frames: ${frames}\nFPS: ${animation.fps.toFixed(0)}\nDelta: ${delta.toFixed(0)}ms`
	);
</script>

<pre>{stats}</pre>
<button onclick={toggle}>
	{animation.running ? "Stop" : "Start"}
</button>
<p>
	FPS limit: <b>{fpsLimit}</b><i>{fpsLimit === 0 ? " (not limited)" : ""}</i>
</p>
<Slider
	value={[fpsLimit]}
	onValueChange={(value) => (fpsLimit = value[0] ?? 0)}
	min={0}
	max={144} />
```
