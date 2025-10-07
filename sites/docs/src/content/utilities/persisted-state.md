---
title: PersistedState
description:
  Web Storage API를 사용하여 브라우저 세션과 탭 간에 상태를 유지하고 동기화하는 반응형 상태 관리자입니다.
category: State
---

<script>
import Demo from '$lib/components/demos/persisted-state.svelte';
import { Callout } from '@svecodocs/kit'
</script>

`PersistedState`는 브라우저 스토리지에 데이터를 자동으로 유지하고 실시간으로 브라우저 탭 간에 변경 사항을 선택적으로 동기화하는 반응형 상태 컨테이너를 제공합니다.

## 데모

<Demo />
<Callout>
	이 페이지를 새로 고침하거나 다른 탭에서 열어 카운트 상태가 세션과 탭 간에 유지되고 동기화되는 것을 확인할 수 있습니다.
</Callout>

## 사용법

고유 키와 상태의 초기값을 제공하여 `PersistedState`를 초기화합니다.

```svelte
<script lang="ts">
	import { PersistedState } from "runed";

	const count = new PersistedState("count", 0);
</script>

<div>
	<button onclick={() => count.current++}>증가</button>
	<button onclick={() => count.current--}>감소</button>
	<button onclick={() => (count.current = 0)}>리셋</button>
	<p>카운트: {count.current}</p>
</div>
```

### 복잡한 객체

복잡한 객체를 유지할 때는 일반 구조만 깊은 반응성을 가집니다.

여기에는 배열, 일반 객체 및 원시 값이 포함됩니다.

예를 들어:

```ts
const persistedArray = new PersistedState("foo", ["a", "b"]);
persistedArray.current.push("c"); // 이 변경은 유지됩니다

const persistedObject = new PersistedState("bar", { name: "Bob" });
persistedObject.current.name = "JG"; // 이 변경은 유지됩니다

class Person {
	name: string;
	constructor(name: string) {
		this.name = name;
	}
}
const persistedComplexObject = new PersistedState("baz", new Person("Bob"));
persistedComplexObject.current.name = "JG"; // 이 변경은 유지되지 않습니다
persistedComplexObject.current = new Person("JG"); // 이 변경은 유지됩니다
```

## 설정 옵션

`PersistedState`는 상태 관리자의 동작을 사용자 정의할 수 있는 `options` 객체를 포함합니다.

```ts
const state = new PersistedState("user-preferences", initialValue, {
	// localStorage 대신 sessionStorage 사용 (기본값: 'local')
	storage: "session",

	// 탭 간 동기화 비활성화 (기본값: true)
	syncTabs: false,

	// 커스텀 직렬화 핸들러
	serializer: {
		serialize: superjson.stringify,
		deserialize: superjson.parse
	}
});
```

### 스토리지 옵션

- `'local'`: 명시적으로 지울 때까지 데이터가 유지됩니다
- `'session'`: 브라우저 세션이 끝날 때까지 데이터가 유지됩니다

### 탭 간 동기화

`syncTabs`가 활성화되면(기본값) 스토리지 이벤트를 사용하여 모든 브라우저 탭에서 변경 사항이 자동으로 동기화됩니다.

### 커스텀 직렬화

복잡한 데이터 유형을 처리하기 위해 커스텀 `serialize` 및 `deserialize` 함수를 제공합니다:

```ts
import superjson from "superjson";

// Date 객체 예제
const lastAccessed = new PersistedState("last-accessed", new Date(), {
	serializer: {
		serialize: superjson.stringify,
		deserialize: superjson.parse
	}
});
```
