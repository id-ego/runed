---
title: Context
description:
  Svelte의 Context API를 감싸서 타입 안전성과 향상된 사용성을 제공하여 컴포넌트 간 데이터를 공유합니다.
category: State
---

<script>
	import { Steps, Step, Callout } from '@svecodocs/kit';
</script>

Context를 사용하면 모든 레벨을 통해 명시적으로 props를 전달하지 않고도 컴포넌트 트리를 통해 데이터를 전달할 수 있습니다. 테마, 인증 상태 또는 로케일 설정과 같이 많은 컴포넌트가 필요로 하는 데이터를 공유하는 데 유용합니다.

`Context` 클래스는 컨텍스트 값을 정의, 설정 및 검색하는 타입 안전한 방법을 제공합니다.

## 사용법

<Steps>

<Step>Context 생성하기</Step>

먼저, 보유할 값의 타입과 함께 `Context` 인스턴스를 생성합니다:

```ts title="context.ts"
import { Context } from "runed";

export const myTheme = new Context<"light" | "dark">("theme");
```

`Context` 인스턴스를 생성하는 것은 컨텍스트를 정의하기만 할 뿐 실제로 값을 설정하지는 않습니다. 생성자에 전달된 값(이 예제에서는 `"theme"`)은 디버깅 및 오류 메시지에 사용되는 식별자일 뿐입니다.

이 단계를 나중에 컨텍스트 값을 보유할 "컨테이너"를 생성하는 것으로 생각하세요. 컨테이너는 타입이 지정되어 있지만(이 경우 `"light"` 또는 `"dark"` 값만 허용) 컴포넌트 초기화 중에 명시적으로 `myTheme.set()`을 호출할 때까지 비어 있습니다.

컨텍스트 정의와 설정 사이의 이러한 분리를 통해 다음을 수행할 수 있습니다:

- 별도의 파일에 컨텍스트 정의를 유지
- 앱의 여러 부분에서 동일한 컨텍스트 정의를 재사용
- 애플리케이션 전체에서 타입 안전성 유지
- 서로 다른 컴포넌트 트리에서 동일한 컨텍스트에 대해 서로 다른 값 설정

<Step>Context 값 설정하기</Step>

초기화 중에 부모 컴포넌트에서 컨텍스트 값을 설정합니다.

```svelte title="+layout.svelte"
<script lang="ts">
	import { myTheme } from "./context";
	let { data, children } = $props();

	myTheme.set(data.theme);
</script>

{@render children?.()}
```

<Callout>

Context는 `onMount`와 같은 라이프사이클 함수와 유사하게 컴포넌트 초기화 중에 설정되어야 합니다.
이벤트 핸들러나 콜백 내부에서는 컨텍스트를 설정할 수 없습니다.

</Callout>

<Step>Context 값 읽기</Step>

자식 컴포넌트는 `get()` 또는 `getOr()`를 사용하여 컨텍스트에 접근할 수 있습니다

```svelte title="+page.svelte"
<script lang="ts">
	import { myTheme } from "./context";

	const theme = myTheme.get();
	// or with a fallback value if the context is not set
	const theme = myTheme.getOr("light");
</script>
```

</Steps>

## 타입 정의

```ts
class Context<TContext> {
	/**
	 * @param name 컨텍스트의 이름입니다.
	 * 컨텍스트 키 생성 및 오류 메시지에 사용됩니다.
	 */
	constructor(name: string) {}

	/**
	 * 컨텍스트를 가져오고 설정하는 데 사용되는 키입니다.
	 *
	 * 이 값을 직접 사용하는 것은 권장되지 않습니다.
	 * 대신 이 클래스에서 제공하는 메서드를 사용하세요.
	 */
	get key(): symbol;

	/**
	 * 부모 컴포넌트의 컨텍스트에 설정되었는지 확인합니다.
	 *
	 * 컴포넌트 초기화 중에 호출되어야 합니다.
	 */
	exists(): boolean;

	/**
	 * 가장 가까운 부모 컴포넌트에 속한 컨텍스트를 검색합니다.
	 *
	 * 컴포넌트 초기화 중에 호출되어야 합니다.
	 *
	 * @throws 컨텍스트가 존재하지 않으면 오류가 발생합니다.
	 */
	get(): TContext;

	/**
	 * 가장 가까운 부모 컴포넌트에 속한 컨텍스트를 검색하거나,
	 * 컨텍스트가 존재하지 않으면 주어진 대체 값을 반환합니다.
	 *
	 * 컴포넌트 초기화 중에 호출되어야 합니다.
	 */
	getOr<TFallback>(fallback: TFallback): TContext | TFallback;

	/**
	 * 현재 컴포넌트와 주어진 값을 연결하고 반환합니다.
	 *
	 * 컴포넌트 초기화 중에 호출되어야 합니다.
	 */
	set(context: TContext): TContext;
}
```
