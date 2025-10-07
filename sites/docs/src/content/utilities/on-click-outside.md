---
title: onClickOutside
description: 지정된 요소 외부의 클릭을 처리합니다.
category: Sensors
---

<script>
import Demo from '$lib/components/demos/on-click-outside.svelte';
import DemoDialog from '$lib/components/demos/on-click-outside-dialog.svelte';
import { PropField } from '@svecodocs/kit'
</script>

`onClickOutside`는 지정된 요소의 경계 외부에서 발생하는 클릭을 감지하고 콜백 함수를 실행합니다. 닫을 수 있는 드롭다운, 모달 및 기타 대화형 컴포넌트에 일반적으로 사용됩니다.

## 데모

<Demo />

## 기본 사용법

```svelte
<script lang="ts">
	import { onClickOutside } from "runed";

	let container = $state<HTMLElement>()!;

	onClickOutside(
		() => container,
		() => console.log("clicked outside")
	);
</script>

<div bind:this={container}>
	<!-- Container content -->
</div>
<button>I'm outside the container</button>
```

## 고급 사용법

### 제어 가능한 리스너

이 함수는 리스너를 프로그래밍 방식으로 관리하기 위한 `start` 및 `stop` 제어 메서드와 리스너의 현재 상태를 확인하기 위한 반응형 읽기 전용 속성 `enabled`를 반환합니다.

```svelte
<script lang="ts">
	import { onClickOutside } from "runed";

	let dialog = $state<HTMLDialogElement>()!;

	const clickOutside = onClickOutside(
		() => dialog,
		() => {
			dialog.close();
			clickOutside.stop();
		},
		{ immediate: false }
	);

	function openDialog() {
		dialog.showModal();
		clickOutside.start();
	}

	function closeDialog() {
		dialog.close();
		clickOutside.stop();
	}
</script>

<button onclick={openDialog}>Open Dialog</button>
<dialog bind:this={dialog}>
	<div>
		<button onclick={closeDialog}>Close Dialog</button>
	</div>
</dialog>
```

다음은 `<dialog>`와 함께 `onClickOutside`를 사용하는 예제입니다:

<DemoDialog />

## 옵션

<PropField name="immediate" type="boolean" defaultValue="true">

클릭 외부 핸들러가 기본적으로 활성화되어 있는지 여부입니다. `false`로 설정하면 반환된 `start` 함수를 호출하여 활성화할 때까지 핸들러가 작동하지 않습니다.

</PropField>

<PropField name="detectIframe" type="boolean" defaultValue="false">

iframe의 포커스 이벤트가 콜백을 트리거할지 여부를 제어합니다. iframe 클릭 이벤트는 부모 문서로 버블링되지 않으므로 사용자가 iframe 콘텐츠와 상호작용하는 시점을 감지해야 하는 경우 이 옵션을 활성화할 수 있습니다.

</PropField>

<PropField name="document" type="Document" defaultValue="document">

사용할 document 객체이며, 기본값은 전역 document입니다.

</PropField>

<PropField name="window" type="Window" defaultValue="window">

사용할 window 객체이며, 기본값은 전역 window입니다.

</PropField>

## 타입 정의

```ts
export type OnClickOutsideOptions = ConfigurableWindow &
	ConfigurableDocument & {
		/**
		 * Whether the click outside handler is enabled by default or not.
		 * If set to false, the handler will not be active until enabled by
		 * calling the returned `start` function
		 *
		 * @default true
		 */
		immediate?: boolean;
		/**
		 * Controls whether focus events from iframes trigger the callback.
		 *
		 * Since iframe click events don't bubble to the parent document,
		 * you may want to enable this if you need to detect when users
		 * interact with iframe content.
		 *
		 * @default false
		 */
		detectIframe?: boolean;
	};
/**
 * A utility that calls a given callback when a click event occurs outside of
 * a specified container element.
 *
 * @template T - The type of the container element, defaults to HTMLElement.
 * @param {MaybeElementGetter<T>} container - The container element or a getter function that returns the container element.
 * @param {() => void} callback - The callback function to call when a click event occurs outside of the container.
 * @param {OnClickOutsideOptions} [opts={}] - Optional configuration object.
 * @param {ConfigurableDocument} [opts.document=defaultDocument] - The document object to use, defaults to the global document.
 * @param {boolean} [opts.immediate=true] - Whether the click outside handler is enabled by default or not.
 * @param {boolean} [opts.detectIframe=false] - Controls whether focus events from iframes trigger the callback.
 *
 * @see {@link https://runed.dev/docs/utilities/on-click-outside}
 */
export declare function onClickOutside<T extends Element = HTMLElement>(
	container: MaybeElementGetter<T>,
	callback: (event: PointerEvent | FocusEvent) => void,
	opts?: OnClickOutsideOptions
): {
	/** Stop listening for click events outside the container. */
	stop: () => boolean;
	/** Start listening for click events outside the container. */
	start: () => boolean;
	/** Whether the click outside handler is currently enabled or not. */
	readonly enabled: boolean;
};
```
