<script context="module">
	export const CAROUSEL = {};
</script>

<script lang="ts">
	import { setContext } from "svelte";

	let self: HTMLElement;

	setContext(CAROUSEL, { registerItem: registerItem });

	let itemUpdates: Function[] = [];

	function registerItem(update: Function): void {
		itemUpdates.push(update);
	}

	function onScroll() {
		let rect: DOMRect = self.getBoundingClientRect();
		for (let update of itemUpdates) {
			update(rect);
		}
	}
</script>

<main bind:this={self} class="main" on:scroll={onScroll}>
	<slot></slot>
</main>

<style>
	.main {
		width: 100%;
		height: 100%;
		border: 1px solid red;
		overflow-y: auto;

		-ms-overflow-style: none;  /* Internet Explorer 10+ */
		scrollbar-width: none;  /* Firefox */
	}

	.main::-webkit-scrollbar { 
		display: none;  /* Safari and Chrome */
	}
</style>