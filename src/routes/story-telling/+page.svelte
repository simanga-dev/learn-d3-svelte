<script lang="ts">
	import data from './data';
	import { scaleLinear } from 'd3-scale';
	// import AxisX from "./AxisX.svelte";
	// import AxisY from "./AxisY.svelte";
	// import Tooltip from "./Tooltip.svelte";

	const margin = {
		top: 20,
		right: 20,
		bottom: 20,
		left: 15
	};

	let width = 400;
	let height = 400;
	$: innerWidth = width - margin.left - margin.right;
	let innerHeight = height - margin.top - margin.bottom;
	$: xScale = scaleLinear().domain([0, 100]).range([0, innerWidth]);
	let yScale = scaleLinear().domain([0, 60]).range([innerHeight, 0]);

	let hoveredData;
	let radius = 10;
</script>

<a class="text-blue-300" href="/">go back to charts</a>

<section>
	<h1>student who study longer do well in exam</h1>
	<div class="chart-container" bind:clientWidth={width}>
	</div>
</section>

<style>
	:global(.tick text, .axis-title) {
		fill: #8f8f8f;
		font-weight: 400;
		font-size: 14px;
	}

	.chart-container {
		position: relative;
	}
	circle {
		transition: r 300ms ease, opacity 500ms ease;
		cursor: pointer;
	}
	h1 {
		font-size: 1.3rem;
		text-transform: capitalize;
		margin-bottom: 0.8rem;
	}
</style>
