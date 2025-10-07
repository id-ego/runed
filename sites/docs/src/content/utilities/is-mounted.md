---
title: IsMounted
description: 호출된 컴포넌트의 마운트 상태를 반환하는 클래스입니다.
category: Component
---

<script>
import Demo from '$lib/components/demos/is-mounted.svelte';
</script>

## 데모

<Demo />

## 사용법

```svelte
<script lang="ts">
	import { IsMounted } from "runed";

	const isMounted = new IsMounted();
</script>
```

다음 중 하나의 축약 표현입니다:

```svelte
<script lang="ts">
	import { onMount } from "svelte";

	const isMounted = $state({ current: false });

	onMount(() => {
		isMounted.current = true;
	});
</script>
```

또는

```svelte
<script lang="ts">
	import { untrack } from "svelte";

	const isMounted = $state({ current: false });

	$effect(() => {
		untrack(() => (isMounted.current = true));
	});
</script>
```
