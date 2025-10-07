---
title: extract
description: getter 또는 정적 변수의 값을 해결합니다
category: Reactivity
---

Runed와 같은 라이브러리에서는 getter(값을 반환하는 함수)를 사용하여 상태를 반응적으로 전달하는 것이 일반적이며, 이는 경계를 넘어 반응성을 전달하는 일반적인 패턴입니다.

```ts
// For example...
import { Previous } from "runed";

let count = $state(0);
const previous = new Previous(() => count);
```

그러나 일부 API는 반응형 getter 또는 정적 값(`undefined` 포함) 중 하나를 허용합니다:

```ts
let search = $state("");
let debounceTime = $state<number | undefined>(500);

// with a reactive value
const d1 = new Debounced(
	() => search,
	() => debounceTime
);

// with a static value
const d2 = new Debounced(() => search, 500);

// no defined value
const d3 = new Debounced(() => search);
```

유틸리티 함수를 작성할 때 두 가지 타입을 모두 처리하면 장황하고 반복적인 로직이 발생할 수 있습니다:

```ts
setTimeout(
    /* ... */,
    typeof wait === "function" ? (wait() ?? 250) : (wait ?? 250)
);
```

이것이 `extract`가 필요한 이유입니다.

## 사용법

`extract` 유틸리티는 getter 또는 정적 값을 일반 값으로 해결합니다. 이를 통해 더 깔끔하고 안전한 유틸리티를 작성할 수 있습니다.

```ts
import { extract } from "runed";

/**
 * Triggers confetti at a given interval.
 * @param intervalProp Time between confetti bursts, in ms. Defaults to 100.
 */
function throwConfetti(intervalProp?: MaybeGetter<number | undefined>) {
	const interval = $derived(extract(intervalProp, 100));
	// ...
}
```

## 동작 방식

`MaybeGetter<T>`가 주어지면 `extract(input, fallback)`는 다음과 같이 해결됩니다:

| 케이스                                      | 결과                        |
| ------------------------------------------- | --------------------------- |
| `input`이 값인 경우                         | 값을 반환                   |
| `input`이 `undefined`인 경우                | fallback을 반환             |
| `input`이 값을 반환하는 함수인 경우         | 함수 결과를 반환            |
| `input`이 `undefined`를 반환하는 함수인 경우| fallback을 반환             |

fallback은 _선택 사항_입니다. 생략하면 `extract()`는 `T | undefined`를 반환합니다.

## 타입

```ts
function extract<T>(input: MaybeGetter<T | undefined>, fallback: T): T;
function extract<T>(input: MaybeGetter<T | undefined>): T | undefined;
```
