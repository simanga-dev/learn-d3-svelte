<script lang="ts">
	import data from './data';
	import {
		forceSimulation,
		forceX,
		forceY,
		forceCollide,
		type SimulationNodeDatum
	} from 'd3-force';
	import { scaleLinear, scaleOrdinal, scaleBand, scaleSqrt } from 'd3-scale';
	import { extent, mean, rollups } from 'd3-array';
	import { z } from 'zod';
	// import AxisX from "./AxisX.svelte";
	// import AxisY from "./AxisY.svelte";
	// import Legend from "./Legend.svelte";
	// import Tootip from "./Tooltip.svelte";
	// import { fade } from "svelte/transition";

	const countrySchema = z.object({
		country: z.string(),
		happiness: z.number(),
		continent: z.string(),
		index: z.number().optional(),
		x: z.number().optional(),
		y: z.number().optional(),
		vx: z.number().optional()
	});

	const contries = z.array(countrySchema).parse(data);

	let width = 400,
		height = 400;

	const margin = {
		top: 15,
		right: 15,
		bottom: 15,
		left: 15
	};

	// Generate the average for each continent, so that we can sort according to that
	const continents = rollups(
		contries,
		(v) => mean(v, (d) => d.happiness),
		(d) => d.continent
	) // Group data by continent and return the group-wide mean
		.sort((a, b) => a[1] - b[1]) // Sort according to value
		.map((d) => d[0]); // Grab the continent name

	$: radiusScale = scaleSqrt()
		.domain(extent(data, (d) => d.happiness))
		.range(width < 568 ? [2, 6] : [3, 8]);

	const colorRange = ['#dda0dd', '#fe7f2d', '#fcca46', '#a1c181', '#619b8a', '#eae2b7'];
	const colorScale = scaleOrdinal().domain(continents).range(colorRange);

	$: innerWidth = width - margin.left - margin.right;
	let innerHeight = height - margin.top - margin.bottom;

	// .domain(extent(data, (d) => d.happiness))
	$: xScale = scaleLinear().domain([1, 9]).range([0, innerWidth]);

	let yScale = scaleBand().domain(continents).range([innerHeight, 0]).paddingOuter(0.5);

	let groupByContinet = false;

	const simulation = forceSimulation(contries);

	$: {
		simulation
			.force(
				'x',
				forceX<z.infer<typeof countrySchema>>()
					.x((d) => xScale(d.happiness))
					.strength(0.8)
			)
			.force(
				'y',
				forceY()
					.y(groupByContinet ? (d) => yScale(d.continent) : innerHeight / 2)
					.strength(0.2)
			)
			.force(
				'collide',
				forceCollide().radius((d) => radiusScale(d.happiness))
			)
			.alpha(0.3)
			.alphaDecay(0.0005)
			.restart();


	let nodes  = simulation.nodes();
	simulation.on('tick', () => {
		nodes = simulation.nodes();
	});

	let hovered;
	let hoveredContinent;
	$: console.log(hoveredContinent);
</script>

<!-- <h1>The happiness continent in the world</h1> -->
<!---->
<!-- <Legend {colorScale} bind:hoveredContinent /> -->
<!-- <div -->
<!--     class="chart-container" -->
<!--     on:click={() => { -->
<!--         groupByContinet = !groupByContinet; -->
<!--         hovered = null; -->
<!--     }} -->
<!--     on:keydown={() => { -->
<!--         groupByContinet = !groupByContinet; -->
<!--         hovered = null; -->
<!--     }} -->
<!--     bind:clientWidth={width} -->
<!-- > -->
<!--     <svg -->
<!--         {width} -->
<!--         {height} -->
<!--         on:mouseleave={() => { -->
<!--             hovered = null; -->
<!--         }} -->
<!--     > -->
<!--         <g transform="translate({margin.left}, {margin.top})"> -->
<!--             <AxisX {xScale} height={innerHeight} width={innerWidth} /> -->
<!--             <AxisY {yScale} {groupByContinet} /> -->
<!--             {#if hovered} -->
<!--                 <line -->
<!--                     x1={hovered.x} -->
<!--                     x2={hovered.x} -->
<!--                     y1={height - margin.bottom} -->
<!--                     y2={hovered.y + margin.top + radiusScale(hovered.happiness)} -->
<!--                     stroke={colorScale(hovered.continent)} -->
<!--                     stroke-width="2" -->
<!--                 /> -->
<!--             {/if} -->
<!---->
<!--             {#each nodes as node, index} -->
<!--                 <circle -->
<!--                     in:fade={{ delay: 500 + index * 10 }} -->
<!--                     role="button" -->
<!--                     cx={node.x} -->
<!--                     cy={node.y} -->
<!--                     r={radiusScale(node.happiness)} -->
<!--                     fill={colorScale(node.continent)} -->
<!--                     stroke={hovered || hoveredContinent -->
<!--                         ? hovered === node || hoveredContinent == node.continent -->
<!--                             ? "black" -->
<!--                             : "transparent" -->
<!--                         : "#00000090"} -->
<!--                     opacity={hovered || hoveredContinent -->
<!--                         ? hovered === node || hoveredContinent == node.continent -->
<!--                             ? 1 -->
<!--                             : 0.3 -->
<!--                         : 1} -->
<!--                     on:mouseover={() => { -->
<!--                         hovered = node; -->
<!--                     }} -->
<!--                     on:focus={() => { -->
<!--                         hovered = node; -->
<!--                     }} -->
<!--                     tabindex="0" -->
<!--                     on:click={(e) => { -->
<!--                         e.stopPropagation(); -->
<!--                     }} -->
<!--                     on:keydown={(e) => { -->
<!--                         e.stopPropagation(); -->
<!--                     }} -->
<!--                 /> -->
<!--             {/each} -->
<!--         </g> -->
<!--     </svg> -->
<!--     {#if hovered} -->
<!--         <Tootip {colorScale} data={hovered} {width} /> -->
<!--     {/if} -->
<!-- </div> -->
<!---->
<!-- <style> -->
<!--     .chart-container { -->
<!--         position: relative; -->
<!--     } -->
<!---->
<!--     circle { -->
<!--         transition: stroke 300ms ease, opacity 300ms ease; -->
<!--         cursor: pointer; -->
<!--     } -->
<!-- </style> -->
<!---->

<!-- <main class="max-w-screen-xl ml-auto pt-4"> -->
<!-- 	<h1 class="text-4xl">D3 Charts</h1> -->
<!-- 	<p>list of d3 charts I build while learning d3</p> -->
<!-- </main> -->
