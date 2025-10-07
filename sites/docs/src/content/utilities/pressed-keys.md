---
title: PressedKeys
description: 현재 눌린 키를 추적합니다
category: Sensors
---

<script>
import Demo from '$lib/components/demos/pressed-keys.svelte';
</script>

## 데모

<Demo />

## 사용법

`PressedKeys` 인스턴스를 사용하여 `has` 메서드를 사용할 수 있습니다.

```ts
const keys = new PressedKeys();

const isArrowDownPressed = $derived(keys.has("ArrowDown"));
const isCtrlAPressed = $derived(keys.has("Control", "a"));
```

또는 현재 눌린 모든 키를 가져올 수 있습니다:

```ts
const keys = new PressedKeys();
console.log(keys.all);
```

또는 지정된 키 조합이 눌렸을 때 실행할 콜백을 등록할 수 있습니다:

```ts
const keys = new PressedKeys();
keys.onKeys(["meta", "k"], () => {
	console.log("open command palette");
});
```
