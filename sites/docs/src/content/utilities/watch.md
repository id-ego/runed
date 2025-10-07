---
title: watch
description: 변경 사항을 감시하고 콜백을 실행합니다
category: Reactivity
---

Runes는 반응형 값이 변경될 때 콜백을 실행하는 편리한 방법을 제공합니다:
[`$effect`](https://svelte.dev/docs/svelte/$effect). 내부 값이 변경되면 자동으로 감지하고 콜백을 다시 실행합니다.

`$effect`는 훌륭하지만, 때로는 어떤 값이 콜백을 트리거해야 하는지 수동으로 지정하고 싶을 수 있습니다. Svelte는 종속성을 추적하지 _말아야_ 한다고 지정할 수 있는 `untrack` 함수를 제공하지만, _특정 값만_ 추적해야 한다고 지정하는 방법은 제공하지 않습니다.

`watch`는 바로 그 역할을 합니다. 효과 콜백의 종속성을 반환하는 getter 함수를 받습니다.

## 사용법

### watch

소스 중 하나가 변경될 때마다 콜백을 실행합니다.

<!-- prettier-ignore -->
```ts
import { watch } from "runed";

let count = $state(0);
watch(() => count, () => {
	console.log(count);
});
```

$state.snapshot()을 사용하여 전체 객체를 깊이 감시할 수 있습니다.

<!-- prettier-ignore -->
```ts
let user = $state({ name: 'bob', age: 20 });
watch(() => $state.snapshot(user), () => {
	console.log(`${user.name}은(는) ${user.age}살입니다`);
});
```

또는 특정 깊은 값을 감시할 수 있습니다.

<!-- prettier-ignore -->
```ts
let user = $state({ name: 'bob', age: 20 });
watch(() => user.age, () => {
	console.log(`사용자는 이제 ${user.age}살입니다`);
});
```

소스 배열을 전달할 수도 있습니다.

<!-- prettier-ignore -->
```ts
let age = $state(20);
let name = $state("bob");
watch([() => age, () => name], ([age, name], [prevAge, prevName]) => {
	// ...
});
```

콜백은 두 개의 인수를 받습니다: 소스의 현재 값과 이전 값입니다.

<!-- prettier-ignore -->
```ts
let count = $state(0);
watch(() => count, (curr, prev) => {
	console.log(`count는 ${curr}이고, 이전에는 ${prev}였습니다`);
});
```

`watch`는 `options` 객체도 받습니다.

```ts
watch(sources, callback, {
	// true로 설정하면 소스가 변경된 후에만 첫 실행이 발생합니다.
	// 기본값은 false입니다.
	lazy: true
});
```

### watch.pre

`watch.pre`는 `watch`와 유사하지만, 내부적으로 [`$effect.pre`](https://svelte.dev/docs/svelte/$effect#$effect.pre)를 사용합니다.

### watchOnce

콜백을 한 번만 실행하려면 `watchOnce`와 `watchOnce.pre`를 사용할 수 있습니다. 이는 `watch` 및 `watch.pre`와 동일하게 작동하지만, options 객체를 받지 않습니다.
