---
title: resource
description: 변경 사항을 감시하고 비동기 데이터 페칭을 실행합니다
category: Reactivity
---

<script>
import Demo from '$lib/components/demos/resource.svelte';
import { Callout } from '@svecodocs/kit'
</script>

SvelteKit에서 load 함수를 사용하는 것이 데이터 페칭의 주요 접근 방식입니다. 쿼리 매개변수에 대해 `URLSearchParams`를 사용하여 반응형 데이터 페칭을 처리할 수 있지만, 컴포넌트 수준에서 더 많은 유연성이 필요한 경우가 있습니다.

여기서 `resource`가 등장합니다 - 반응형 상태 관리와 비동기 데이터 페칭을 원활하게 결합하는 유틸리티입니다.

`watch` 위에 구축되어 기본적으로 렌더링 후에 실행되지만, `resource.pre()`를 통해 사전 렌더링 옵션도 제공합니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { resource } from "runed";

	let id = $state(1);

	const searchResource = resource(
		() => id,
		async (id, prevId, { data, refetching, onCleanup, signal }) => {
			// data: fetcher에서 반환된 이전 값

			// refetching: fetcher가 현재 다시 페칭 중인지 여부
			// 또는 refetch()에 전달한 값일 수 있습니다

			// onCleanup: 소스가 무효화되고 fetcher가 다시 실행되기 전에 호출될 정리 함수

			// signal: fetch 요청을 취소하기 위한 AbortSignal
			const response = await fetch(`api/posts?id=${id}`, { signal });
			return response.json();
		},
		{
			debounce: 300
			// lazy: true일 때 초기 페칭 건너뛰기
			// once: true일 때 한 번만 페칭
			// initialValue: 리소스에 대한 초기 값 제공
			// debounce: 빠른 변경 디바운스
			// throttle: 빠른 변경 쓰로틀
		}
	);

	// 리소스의 현재 값
	searchResource.current;
	// 리소스가 현재 로딩 중인지 여부
	searchResource.loading;
	// 페칭이 실패한 경우 에러
	searchResource.error;
	// 리소스 값을 직접 업데이트, 낙관적 업데이트에 유용
	searchResource.mutate();
	// 현재 감시 중인 값으로 fetcher 다시 실행
	searchResource.refetch();
</script>

<input type="number" bind:value={id} />

{#if searchResults.loading}
	<div>로딩 중...</div>
{:else if searchResults.error}
	<div>에러: {searchResults.error.message}</div>
{:else}
	<ul>
		{#each searchResults.current ?? [] as result}
			<li>{result.title}</li>
		{/each}
	</ul>
{/if}
```

## 기능

- **자동 요청 취소**: 의존성이 변경되면 진행 중인 요청이 자동으로 취소됩니다
- **로딩 및 에러 상태**: 로딩 및 에러 처리를 위한 내장 상태
- **디바운싱 및 쓰로틀링**: 속도 제한을 위한 선택적 디바운스 및 쓰로틀 옵션
- **타입 안전성**: 추론된 타입과 함께 완전한 TypeScript 지원
- **다중 의존성**: 여러 반응형 의존성 추적 지원
- **커스텀 정리**: 다시 페칭하기 전에 실행되는 정리 함수 등록
- **사전 렌더링 지원**: 사전 렌더링 실행을 위한 `resource.pre()`

## 고급 사용법

### 다중 의존성

```svelte
<script lang="ts">
	const results = resource([() => query, () => page], async ([query, page]) => {
		const res = await fetch(`/api/search?q=${query}&page=${page}`);
		return res.json();
	});
</script>
```

### 커스텀 정리

```svelte
<script lang="ts">
	const stream = resource(
		() => streamId,
		async (id, _, { signal, onCleanup }) => {
			const eventSource = new EventSource(`/api/stream/${id}`);
			onCleanup(() => eventSource.close());

			const res = await fetch(`/api/stream/${id}/init`, { signal });
			return res.json();
		}
	);
</script>
```

### 사전 렌더링 실행

```svelte
<script lang="ts">
	const data = resource.pre(
		() => query,
		async (query) => {
			const res = await fetch(`/api/search?q=${query}`);
			return res.json();
		}
	);
</script>
```

## 구성 옵션

### `lazy`

true일 때 초기 페칭을 건너뜁니다. 리소스는 의존성이 변경되거나 `refetch()`가 호출될 때만 페칭합니다.

### `once`

true일 때 한 번만 페칭합니다. 이후 의존성 변경은 새로운 페칭을 트리거하지 않습니다.

### `initialValue`

첫 번째 페칭이 완료되기 전에 리소스에 대한 초기 값을 제공합니다. 이미 데이터가 있고 페칭이 완료될 때까지 기다리고 싶지 않은 경우 유용합니다.

### `debounce`

빠른 변경을 디바운스할 시간(밀리초)입니다. 검색 입력에 유용합니다.

디바운스 구현은 대기 중인 요청을 취소하고 지정된 지연 후에 마지막 요청만 실행합니다.

### `throttle`

빠른 변경을 쓰로틀할 시간(밀리초)입니다. 실시간 업데이트에 유용합니다.

쓰로틀 구현은 요청이 지정된 지연 시간만큼 최소한 간격을 두도록 보장하며, 너무 빨리 호출되면 대기 중인 프라미스를 반환합니다.

<Callout>
디바운스 또는 쓰로틀 중 하나만 사용해야 합니다. 둘 다 지정하면 디바운스가 우선합니다.
</Callout>

## 타입 정의

```ts
type ResourceOptions<Data> = {
	/** Skip initial fetch when true */
	lazy?: boolean;
	/** Only fetch once when true */
	once?: boolean;
	/** Initial value for the resource */
	initialValue?: Data;
	/** Debounce time in milliseconds */
	debounce?: number;
	/** Throttle time in milliseconds */
	throttle?: number;
};

type ResourceState<Data> = {
	/** Current value of the resource */
	current: Data | undefined;
	/** Whether the resource is currently loading */
	loading: boolean;
	/** Error if the fetch failed */
	error: Error | undefined;
};

type ResourceReturn<Data, RefetchInfo = unknown> = ResourceState<Data> & {
	/** Update the resource value directly */
	mutate: (value: Data) => void;
	/** Re-run the fetcher with current values */
	refetch: (info?: RefetchInfo) => Promise<Data | undefined>;
};

type ResourceFetcherRefetchInfo<Data, RefetchInfo = unknown> = {
	/** Previous data return from fetcher */
	data: Data | undefined;
	/** Whether the fetcher is currently refetching or it can be the value you passed to refetch. */
	refetching: RefetchInfo | boolean;
	/** A cleanup function that will be called when the source is invalidated and the fetcher is about to re-run */
	onCleanup: (fn: () => void) => void;
	/** AbortSignal for cancelling fetch requests */
	signal: AbortSignal;
};

type ResourceFetcher<Source, Data, RefetchInfo = unknown> = (
	/** Current value of the source */
	value: Source extends Array<unknown>
		? {
				[K in keyof Source]: Source[K];
			}
		: Source,
	/** Previous value of the source */
	previousValue: Source extends Array<unknown>
		? {
				[K in keyof Source]: Source[K];
			}
		: Source | undefined,
	info: ResourceFetcherRefetchInfo<Data, RefetchInfo>
) => Promise<Data>;

function resource<
	Source,
	RefetchInfo = unknown,
	Fetcher extends ResourceFetcher<
		Source,
		Awaited<ReturnType<Fetcher>>,
		RefetchInfo
	> = ResourceFetcher<Source, any, RefetchInfo>
>(
	source: Getter<Source>,
	fetcher: Fetcher,
	options?: ResourceOptions<Awaited<ReturnType<Fetcher>>>
): ResourceReturn<Awaited<ReturnType<Fetcher>>, RefetchInfo>;
```
