<script lang="ts">
	import { getContext } from "svelte";
	import { CAROUSEL } from "./Carousel.svelte";

	let self: HTMLElement;

	export let height: number = 100;

	let scroll = 0;
	let radius = 0;

	const { registerItem, getRadius, margin, tiltFix } = getContext(CAROUSEL);

	registerItem(update, height+margin);

	function update(first: boolean, angle: number, parentScroll: number): number {
		let pRadius = getRadius();
		let theta = 2*Math.asin(((height+margin)/2)/pRadius);
		radius = Math.sqrt((pRadius*pRadius)-Math.pow(height/2,2));
		scroll = parentScroll - angle - (first ? 0: (theta/2));
		return theta;
	}

	function opac(v: number) {
		if (Math.abs(v) > Math.PI/4) {
			return 0;
		} else {
			return Math.min(1, Math.cos(2*v)+0.1);
		}
	}

	function lerp(a: number, b: number, t: number) {
		return a+(b-a)*t;
	}

	function adjustAngle(v: number) {
		v = ((v+Math.PI/4)%(2*Math.PI))-Math.PI/4
		if (Math.abs(v) <= Math.PI/4) {
			let d = Math.abs(v)/(Math.PI/4);
			return tiltFix*lerp(-v, 0, d);
		} else {
			return 0;
		}
	}

	function events(v: number) {
		return Math.abs(v) > Math.PI/4;
	}
</script>

<main class="main" bind:this={self} style="opacity: {opac(scroll)}; height: {height}px; transform: translateY(-50%) perspective(500px) translateZ({-radius}px) rotateX({scroll}rad) translateZ({radius}px) rotateX({adjustAngle(scroll)}rad); {events(scroll) ? "pointer-events: none;" : ""}">
	<slot></slot>
</main>

<style>
	.main {
		position: absolute;
		width: 100%;
		margin: auto;
		top: 50%;
		left: 0;
	}
</style>