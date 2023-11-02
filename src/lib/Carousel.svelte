<script context="module">
	export const CAROUSEL = {};
</script>

<script lang="ts">
	import { setContext, onMount, onDestroy } from "svelte";

	export let margin: number = 200;
	export let scrollSensitivity: number = 2;
	export let scrollProgression: number = 0;
	export let maxWidth: string = "800px";
	export let tiltFix: number = 1;

	scrollSensitivity = 1000/scrollSensitivity;

	let radius: number = 400;

	let self: HTMLElement;

	let scroll: number = 0;
	let scrollV: number = 0;

	setContext(CAROUSEL, { registerItem: registerItem, getRadius: () => {return radius}, margin: margin, tiltFix: tiltFix });

	let itemUpdates: Function[] = [];

	function registerItem(update: Function, height: number): void {
		itemUpdates.push(update);
	}

	function onScroll(e: WheelEvent) {
		scrollV += 20*e.deltaY/radius;
	}

	onMount(() => {
		setInterval(() => {
			let rect: DOMRect = self.getBoundingClientRect();
			radius = rect.height;

			let angle: number = 0;
			let scrollAngle: number = scroll/scrollSensitivity;
			
			let first = true;
			for (let update of itemUpdates) {
				angle += update(first, angle, scrollAngle);
				if (first) {
					angle /= 2;
					first = false;
				}
			}

			if (scrollAngle > angle) {
				scroll = angle*scrollSensitivity;
			} else if (scrollAngle < 0) {
				scroll = 0;
			}

			scrollProgression = scrollAngle/angle;

			scroll += scrollV;
			scrollV -= 0.05*scrollV;
		}, 8.333333)
	});
</script>

<main bind:this={self} class="main" on:wheel={onScroll}>
	<div class="container" style="max-width: {maxWidth};">
		<slot></slot>
	</div>
</main>

<style>
	.main {
		width: calc(100%-200px);
		height: 100%;
		padding-left: 20px;
		padding-right: 20px;
		overflow: hidden;

		-ms-overflow-style: none;  /* Internet Explorer 10+ */
		scrollbar-width: none;  /* Firefox */
	}

	.main::-webkit-scrollbar { 
		display: none;  /* Safari and Chrome */
	}

	.container {
		height: 100%;
		position: relative;
		overflow: visible;
		margin: auto;
		margin-left: auto;
		margin-right: auto;
	}
</style>