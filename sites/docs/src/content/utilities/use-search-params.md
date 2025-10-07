---
title: useSearchParams
description: Svelte/SvelteKit을 위한 반응형, 스키마 검증 URL 검색 파라미터
category: Reactivity
---

<script>
import Demo from '$lib/components/demos/use-search-params.svelte';
import { Callout } from '@svecodocs/kit';
</script>

`useSearchParams`는 Svelte/SvelteKit 앱에서 URL 검색 파라미터를 관리하는 반응형, 타입 안전, 스키마 기반 방법을 제공합니다.
검증, 기본값, 압축, 디바운싱 및 히스토리 제어를 지원합니다.

## 데모

<Demo />
<Callout>
	이 페이지를 새로고침하거나 다른 탭에서 열어 세션과 탭 간에 카운트 상태가 유지되고 동기화되는 것을 확인할 수 있습니다.
</Callout>

## 요구사항

- **`@sveltejs/kit`**이 프로젝트에 설치되어 있어야 합니다.
- 스키마 검증 및 타입 추론을 위해 [Standard Schema](https://standardschema.dev/)를 사용합니다.

## 사용법

스키마 정의:

```ts
// schemas.ts
import { z } from "zod";
// Version 3.24.0+

export const productSearchSchema = z.object({
	page: z.coerce.number().default(1),
	filter: z.string().default(""),
	sort: z.enum(["newest", "oldest", "price"]).default("newest")
});
```

Svelte 코드에서:

```svelte
<script lang="ts">
import { useSearchParams } from "runed/kit";
import { productSearchSchema } from './schemas';

const params = useSearchParams(productSearchSchema);
// 파라미터에 직접 액세스
const page = $derived(params.page); // number (기본값 1)
const sort = $derived(params.sort); // 'newest' | 'oldest' | 'price'

// 파라미터 직접 업데이트
params.page = 2; // URL을 ?page=2로 업데이트
// URL을 ?page=3&sort=oldest로 업데이트
params.update({ page: 3, sort: 'oldest' });
// 모든 파라미터를 기본값으로 재설정
params.reset();
// 모든 현재 파라미터 값을 포함하는 URLSearchParams 객체 반환
params.toURLSearchParams();
/**
 * URLSearchParams 객체의 변경을 감시할 수 있습니다
 * 예를 들어:
 * watch(() => params.toURLSearchParams(), () => {
 *   // URLSearchParams 객체가 변경될 때마다 수행할 작업
 * })
 */
</script>

<-- 입력 필드에 바인딩하기 좋습니다 -->
<input type="text" bind:value={params.filter} />
```

로드 함수에서:

```ts
import { validateSearchParams } from "runed/kit";
import { productSearchSchema } from "./schemas";

export const load = ({ url, fetch }) => {
	// URLSearchParams 객체로 검증된 검색 파라미터 가져오기
	// useSearchParams에서 사용자 정의 compressedParamName을 사용하는 경우 여기에도 제공하세요:
	const { searchParams } = validateSearchParams(url, productSearchSchema);

	// fetch와 함께 URLSearchParams 직접 사용
	const response = await fetch(`/api/products?${searchParams.toString()}`);
	return {
		products: await response.json()
	};
};
```

## 기능

- **스키마 검증**: 각 파라미터의 타입, 기본값, 구조를 정의합니다.
- **기본값**: 누락된 파라미터는 자동으로 기본값으로 채워집니다.
- **타입 안전성**: 모든 값이 스키마에 따라 파싱되고 검증됩니다.
- **압축**: 더 깔끔한 URL을 위해 모든 파라미터를 단일 압축 `_data` 파라미터에 저장합니다.
- **디바운싱**: 더 부드러운 UX를 위해 선택적으로 업데이트를 디바운싱합니다.
- **히스토리 제어**: push/replace 상태 중에서 선택합니다.
- **인메모리 모드**: 파라미터가 URL이 아닌 메모리에 유지됩니다.
- **잘못된 파라미터 처리**: 잘못된 값은 기본값으로 대체되고 URL에서 제거됩니다.

### `useSearchParams`

속성 액세스를 통한 반응형 검색 파라미터 객체를 생성하는 훅

이 클라이언트 측 훅은 파라미터가 변경되면 자동으로 URL을 업데이트합니다. 직접 속성 액세스를 통해 URL 검색 파라미터에 대한 타입 안전 액세스를 제공합니다.

**파라미터:**

- `schema`: StandardSchemaV1과 호환되는 검증 스키마
- `options`: URL 동작에 영향을 주는 구성 옵션

**반환값:**

- 타입이 지정된 검색 파라미터 작업을 위한 반응형 객체

#### 사용 가능한 옵션:

- `showDefaults` (boolean): true일 때 기본값을 가진 파라미터가 URL에 표시됩니다. false(기본값)일 때 기본값을 가진 파라미터는 URL에서 생략됩니다.
- `debounce` (number): 파라미터가 변경될 때 URL 업데이트를 지연시킬 밀리초. 값이 빠르게 변경될 때 브라우저 히스토리가 복잡해지는 것을 방지하는 데 유용합니다(기본값: 0, 디바운싱 없음).
- `pushHistory` (boolean): URL 업데이트가 새로운 브라우저 히스토리 항목을 생성할지 제어합니다. true(기본값)일 때 각 업데이트가 브라우저 히스토리에 새 항목을 추가합니다. false일 때 업데이트는 새 히스토리 항목을 생성하지 않고 현재 URL을 교체합니다.
- `compress` (boolean): true일 때 모든 파라미터가 lz-string 압축을 사용하여 단일 파라미터로 압축됩니다. 이는 URL 길이를 줄이고 기본적인 난독화를 제공하는 데 도움이 됩니다(기본값: false). 서버 측에서 압축된 URL을 처리할 때 동일한 compressedParamName 옵션과 함께 validateSearchParams를 사용하세요.
- `compressedParamName` (string): 압축이 활성화될 때 압축된 데이터를 저장하는 데 사용되는 파라미터의 이름. 스키마의 파라미터와의 충돌을 피하기 위해 이를 사용자 정의할 수 있습니다. 기본값은 `_data`입니다.
- `updateURL` (boolean): true(기본값)일 때 파라미터가 변경되면 URL이 업데이트됩니다. false일 때는 인메모리 파라미터만 업데이트됩니다.

[Zod](https://zod.dev/)를 사용한 예제:

```ts
import { z } from "zod";

const productSearchSchema = z.object({
	page: z.number().default(1),
	filter: z.string().default(""),
	sort: z.enum(["newest", "oldest", "price"]).default("newest")
});
const params = useSearchParams(productSearchSchema);
// 파라미터에 직접 액세스
const page = $derived(params.page); // number (기본값 1)
const sort = $derived(params.sort); // 'newest' | 'oldest' | 'price'
```

옵션을 사용한 예제:

```ts
// URL에 기본값 표시, 300ms 디바운싱,
// 새로운 히스토리 항목 생성 안 함, 파라미터 압축
const params = useSearchParams(schema, {
  showDefaults: true,
  debounce: 300,
  pushHistory: false,
  compress: true,
  compressedParamName: '_compressed' // 충돌을 피하기 위한 사용자 정의 이름
});
// 입력 필드에 바인딩하기 좋습니다 (히스토리를 복잡하게 하지 않고 URL 업데이트)
<input type="text" bind:value={params.search} />
// 결과 URL은 다음과 같습니다: /?_compressed=N4IgDgTg9g...
```

[Valibot](https://valibot.dev/)을 사용한 예제:

```ts
import * as v from "valibot";
const productSearchSchema = v.object({
	page: v.optional(v.fallback(v.number(), 1), 1),
	filter: v.optional(v.fallback(v.string(), ""), ""),
	sort: v.optional(v.fallback(v.picklist(["newest", "oldest", "price"]), "newest"), "newest")
});
const params = useSearchParams(productSearchSchema);
```

[Arktype](https://arktype.io/)을 사용한 예제:

```ts
import { type } from "arktype";
const productSearchSchema = type({
	page: "number = 1",
	filter: 'string = ""',
	sort: '"newest" | "oldest" | "price" = "newest"'
});
const params = useSearchParams(productSearchSchema);
```

또는 내장 스키마 생성기 사용 (추가 종속성 없음)

### `createSearchParamsSchema`

외부 검증 라이브러리 없이 [Standard Schema](https://standardschema.dev/)와 호환되는 간단한 스키마를 생성합니다.

이는 Zod, Valibot, Arktype과 같은 완전한 스키마 검증 라이브러리를 사용하는 것에 대한 경량 대안입니다. 종속성을 추가하지 않고 기본적인 타입 변환과 기본값이 필요한 경우 이를 사용하세요.

제한사항:

- 'array' 타입의 경우: 기본 배열을 지원하지만 배열 항목을 검증하지 않습니다
- 'object' 타입의 경우: 일반 객체를 지원하지만 중첩된 속성을 검증하지 않습니다
- 사용자 정의 검증 규칙이나 변환이 없습니다
- **세밀한 반응성 없음**: 중첩된 속성에 대한 변경은 전체 값을 재할당해야 합니다
  - ❌ `params.config.theme = 'dark'` (URL 업데이트를 트리거하지 않음)
  - ✅ `params.config = {...params.config, theme: 'dark'}` (URL 업데이트 트리거)

복잡한 검증이 필요한 경우(중첩 검증, 정제된 규칙 등) 전용 검증 라이브러리를 대신 사용하세요.

```ts
const productSearchSchema = createSearchParamsSchema({
	// 기본값이 있는 기본 타입
	page: { type: "number", default: 1 },
	filter: { type: "string", default: "" },
	sort: { type: "string", default: "newest" },

	// 특정 요소 타입을 가진 배열 타입
	tags: {
		type: "array",
		default: ["new"],
		arrayType: "" // string[] 타입 지정
	},

	// 특정 형태를 가진 객체 타입
	config: {
		type: "object",
		default: { theme: "light" },
		objectType: { theme: "" } // { theme: string } 타입 지정
	}
});
```

URL 저장 형식:

- 배열은 JSON 문자열로 저장됩니다: `?tags=["sale","featured"]`
- 객체는 JSON 문자열로 저장됩니다: `?config={"theme":"dark","fontSize":14}`
- 기본 값은 직접 저장됩니다: `?page=2&filter=red`

### `validateSearchParams`

URL 검색 파라미터를 추출, 검증 및 [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)로 변환하는 유틸리티 함수

이 함수는 클라이언트 측 컴포넌트(`useSearchParams` 사용)와 서버 측 로드 함수 모두에서 동일한 스키마 검증을 쉽게 사용할 수 있게 합니다. `useSearchParams`와 달리 이 함수는 URL을 수정하지 않습니다 - 파라미터만 검증하고 새로운 URLSearchParams 객체로 반환합니다.

표준 URL 파라미터와 압축된 파라미터(압축이 활성화된 경우) 모두 처리합니다.

**파라미터:**

- `url`: SvelteKit 로드 함수의 URL 객체
- `schema`: 검증 스키마 (createSearchParamsSchema, Zod, Valibot 등)
- `options`: 선택적 구성 (사용자 정의 `compressedParamName` 같은)

**반환값:**

- `searchParams`와 `data` 속성을 가진 객체, `searchParams`는 검증된 `URLSearchParams`이고 `data`는 검증된 객체입니다

SvelteKit 페이지 또는 레이아웃 로드 함수를 사용한 예제:

```ts
import { validateSearchParams } from "runed/kit";
import { productSchema } from "./schemas";

export const load = ({ url, fetch }) => {
	// URLSearchParams 객체로 검증된 검색 파라미터 가져오기
	// useSearchParams에서 사용자 정의 compressedParamName을 사용하는 경우 여기에도 제공하세요:
	const { searchParams } = validateSearchParams(url, productSchema, {
		compressedParamName: "_compressed"
	});

	// fetch와 함께 URLSearchParams 직접 사용
	const response = await fetch(`/api/products?${searchParams.toString()}`);
	return {
		products: await response.json()
	};
};
```

## 반응성 제한사항

### 반응성 범위 이해

`useSearchParams`는 **최상위 반응성만** 제공합니다. 즉:

✅ **작동함 (직접 속성 할당)**:

```svelte
<script>
	const params = useSearchParams(schema);

	// 이들은 URL 업데이트를 트리거합니다
	params.page = 2;
	params.filter = "active";
	params.config = { theme: "dark", size: "large" };
	params.items = [...params.items, newItem];
</script>
```

❌ **작동하지 않음 (중첩 속성 변경)**:

```svelte
<script>
	const params = useSearchParams(schema);

	// 이들은 URL 업데이트를 트리거하지 않습니다
	params.config.theme = "dark"; // 중첩된 객체 속성
	params.items.push(newItem); // 배열 메서드
	params.items[0].name = "updated"; // 배열 항목 속성
	delete params.config.oldProp; // 속성 삭제
</script>
```

### 이런 설계를 선택한 이유

`useSearchParams`는 깊은 반응성보다 **단순성, 타입 안전성, 사용 편의성**을 우선시하도록 설계되었습니다. 이 설계 선택은 여러 가지 이점을 제공합니다:

#### ✅ **얻을 수 있는 것**

- **간단하고 예측 가능한 API**: `params.page = 2`가 항상 작동합니다
- **완전한 TypeScript 지원**: 완벽한 자동완성 및 타입 검사
- **깔끔한 URL**: 객체가 읽기 쉬운 JSON 문자열로 직렬화됩니다
- **성능**: 깊은 객체 변경 추적으로 인한 오버헤드가 없습니다
- **신뢰성**: 복잡한 중첩 프록시 동작에 대한 엣지 케이스가 없습니다

## 타입 정의

```ts
interface SearchParamsOptions {
	/**
	 * true일 때, 기본값으로 설정된 파라미터가 URL에 표시됩니다.
	 * false일 때, 기본값을 가진 파라미터는 URL에서 생략됩니다.
	 * @default false
	 */
	showDefaults?: boolean;

	/**
	 * 파라미터가 변경될 때 URL 업데이트를 지연시킬 밀리초 수.
	 * 값이 빠르게 변경될 때(예: 입력 필드에 타이핑 중) 브라우저 히스토리가 복잡해지는 것을 방지하는 데 도움이 됩니다.
	 * @default 0 (디바운싱 없음)
	 */
	debounce?: number;

	/**
	 * URL 업데이트가 새로운 브라우저 히스토리 항목을 생성할지 제어합니다.
	 * true(기본값)일 때, 각 업데이트가 브라우저 히스토리에 새 항목을 추가합니다.
	 * false일 때, 업데이트는 새 히스토리 항목을 생성하지 않고 현재 URL을 교체합니다.
	 * @default true
	 */
	pushHistory?: boolean;

	/**
	 * 모든 파라미터에 대해 lz-string 압축을 활성화합니다.
	 * true일 때, 모든 파라미터가 URL의 단일 파라미터로 압축됩니다.
	 * 이는 URL 길이를 줄이고 기본적인 파라미터 난독화를 제공하는 데 도움이 됩니다.
	 * @default false
	 */
	compress?: boolean;

	/**
	 * 압축이 활성화될 때 압축된 데이터를 저장하는 데 사용되는 파라미터의 이름.
	 * 스키마 파라미터와의 충돌을 피하기 위해 이를 사용자 정의할 수 있습니다.
	 * 예를 들어, 스키마가 이미 '_data'를 사용하는 경우 '_compressed' 또는 다른 고유한 이름을 사용할 수 있습니다.
	 * @default '_data'
	 */
	compressedParamName?: string;

	/**
	 * 파라미터가 변경될 때 URL을 업데이트할지 제어합니다.
	 * true(기본값)일 때, 파라미터 변경이 URL을 업데이트합니다.
	 * false일 때, 파라미터는 URL을 업데이트하지 않고 메모리에만 저장됩니다.
	 * 참고: false일 때 compress 옵션은 무시됩니다.
	 * @default true
	 */
	updateURL?: boolean;
}

type ReturnUseSearchParams<Schema extends StandardSchemaV1> = {
	[key: string]: any; // 타입이 지정된 반응형 파라미터
	/**
	 * 여러 파라미터를 한 번에 업데이트
	 *
	 * 하나의 URL 업데이트 또는 하나의 인메모리 저장소 업데이트만 트리거하기 때문에
	 * 여러 파라미터를 개별적으로 설정하는 것보다 효율적입니다.
	 *
	 * @param values 업데이트할 파라미터 키-값 쌍을 포함하는 객체.
	 *  예: params.update({ page: 1, sort: 'newest' })
	 */
	update(values: Partial<StandardSchemaV1.InferOutput<Schema>>): void;
	/**
	 * 모든 파라미터를 기본값으로 재설정
	 *
	 * 이 메서드는 모든 현재 URL 파라미터 또는 인메모리 파라미터를 제거하고
	 * 선택적으로 기본값이 아닌 값을 가진 파라미터를 기본값으로 되돌립니다.
	 *
	 * @param showDefaults 재설정 후 URL 또는 인메모리 저장소에 기본값을 표시할지 여부.
	 * 제공되지 않으면 인스턴스의 showDefaults 옵션을 사용합니다.
	 */
	reset(showDefaults?: boolean): void;
	/**
	 * 현재 스키마 파라미터를 URLSearchParams 객체로 변환
	 * URL에 있는지 여부에 관계없이 스키마에 정의된 모든 값이 포함됩니다
	 * @returns 모든 현재 파라미터 값을 포함하는 URLSearchParams 객체
	 */
	toURLSearchParams(): URLSearchParams;
};

/**
 * createSearchParamsSchema를 위한 스키마 타입
 * 배열과 객체에 대해 더 정확한 타입을 지정할 수 있습니다
 */
type SchemaTypeConfig<ArrayType = unknown, ObjectType = unknown> =
	| { type: "string"; default?: string }
	| { type: "number"; default?: number }
	| { type: "boolean"; default?: boolean }
	| { type: "array"; default?: ArrayType[]; arrayType?: ArrayType }
	| { type: "object"; default?: ObjectType; objectType?: ObjectType };
```
