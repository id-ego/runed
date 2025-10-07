---
title: useDebounce
description: 함수의 실행을 디바운스하는 고차 함수입니다.
category: Utilities
---

<script>
	import Demo from '$lib/components/demos/use-debounce.svelte';
</script>

`useDebounce`는 콜백 함수의 디바운스 버전을 생성하는 유틸리티 함수입니다.
디바운싱은 지정된 비활성 기간이 지난 후까지 실행을 지연시켜 함수가 너무 자주 호출되는 것을 방지합니다.

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { useDebounce } from "runed";

	let count = $state(0);
	let logged = $state("");
	let isFirstTime = $state(true);
	let debounceDuration = $state(1000);

	const logCount = useDebounce(
		() => {
			if (isFirstTime) {
				isFirstTime = false;
				logged = `버튼을 ${count}번 눌렀습니다!`;
			} else {
				logged = `지난번 이후 버튼을 ${count}번 눌렀습니다!`;
			}
			count = 0;
		},
		() => debounceDuration
	);

	function ding() {
		count++;
		logCount();
	}
</script>

<input type="number" bind:value={debounceDuration} />
<button onclick={ding}>딩딩딩</button>
<button onclick={logCount.runScheduledNow} disabled={!logCount.pending}>지금 실행</button>
<button onclick={logCount.cancel} disabled={!logCount.pending}>메시지 취소</button>
<p>{logged || "버튼을 누르세요!"}</p>
```
