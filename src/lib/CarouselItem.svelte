<script lang="ts">
	import { getContext } from "svelte";
	import { CAROUSEL } from "./Carousel.svelte";

	let self: HTMLElement;

	const { registerItem } = getContext(CAROUSEL);

	registerItem(update);

	let alpha: number = 0;
	let yOffset: number = 0;
	let lag: number = 3;
	let padding: number = 100;

	function update(pRect: DOMRect): void {
		let rect: DOMRect = self.getBoundingClientRect();

		let v: number = 1-(1/lag);

		let top: number = Math.min(1, v+(rect.bottom - (pRect.top+padding))/rect.height/lag);
		let bottom: number = Math.min(1, v+((pRect.bottom-padding) - rect.top)/rect.height/lag);
		alpha = Math.max(top*bottom, 0);
		yOffset = 140*(bottom-top);
	}
</script>

<main class="main" bind:this={self}>
	<!-- {alpha} -->
	<div class="transform" style="z-index: {yOffset < 0 ? -1 : 1}; opacity: {alpha}; transform: translateY({yOffset}px) scale({Math.pow(alpha, 0.2)});">
		<slot></slot>
	</div>
	<!-- {alpha} -->
</main>

<style>
	.main {
		border: 1px solid blue;
	}

	.transform {
		position: relative;
		border: 1px solid purple;
		background-color: violet;
	}
</style>