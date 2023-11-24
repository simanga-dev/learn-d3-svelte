<script lang="ts">
	import world from './world.json';
	import type { Topology, GeometryObject } from 'topojson-specification';
	import * as topojson from 'topojson-client';
	import data from './data_p.json';
	import { geoOrthographic, geoPath, geoCentroid } from 'd3-geo';
	import Glow from './Glow.svelte';
	import ToolTip from './Tooltip.svelte';
	import Legend from './Legend.svelte';
	import { scaleLinear } from 'd3-scale';
	import { max } from 'd3-array';
	import { onMount } from 'svelte';
	import { select } from 'd3-selection';
	import { drag } from 'd3-drag';
	import { timer } from 'd3-timer';
	import { spring } from 'svelte/motion';
	import { draw } from 'svelte/transition';

	// const countries = topojson.feature(world, world.objects.contries).features

	let countries = topojson.feature(
		world as unknown as Topology,
		world.objects.countries as GeometryObject
	).features;

	let borders = topojson.mesh(
		world as unknown as Topology,
		world.objects.countries as GeometryObject,
		(a: GeometryObject, b: GeometryObject) => a !== b
	);

	let toolTipData: any;

    console.log(countries)

	countries.forEach((country: any) => {
		const metadata = data.find((d) => d.id === country.id);
		if (metadata) {
			country.population = metadata.population;
			country.country = metadata.country;
		}
	});

	let xRotation = spring(0, {
		stiffness: 0.08,
		damping: 0.4
	});

	let yRotation = spring(-15, {
		stiffness: 0.1,
		damping: 0.7
	});

	let degressPerFrame = 0.5;

	let width = 400;
	$: height = width;

	$: projection = geoOrthographic()
		.scale(width / 2)
		.rotate([$xRotation, $yRotation])
		// .rotate([0, 0, 0])
		.translate([width / 2, height / 2]);

	$: path = geoPath(projection);

	const DRAG_SENSENTIVITY = 0.5;
	let dragging = false;

	let maxPopulation = max(data, (d) => d.population);
	const colorScale = scaleLinear<string>()
		.domain([0, maxPopulation ? maxPopulation : 0])
		.range(['#26362e', '#0DCC6C']);

	const t = timer((elapsed) => {
		if (dragging || toolTipData) return;
		$xRotation += degressPerFrame;
	}, 1);

	let globe: SVGElement;
	onMount(() => {
		const element = select(globe);
		element.call(
			drag()
				.on('drag', (event: any) => {
					$xRotation = $xRotation + event.dx * DRAG_SENSENTIVITY;
					$yRotation = $yRotation - event.dy * DRAG_SENSENTIVITY;
					dragging = true;
				})
				.on('end', () => {
					dragging = false;
				}) as any
		);
	});

	$: if (toolTipData) {
		const center = geoCentroid(toolTipData);
		$xRotation = -center[0];
		$yRotation = -center[1];
	}
</script>

<a class="text-blue-300" href="/">go back to charts</a>

<section>
	<div class="chart-container" bind:clientWidth={width}>
		<h1>The world at glance</h1>
		<h2>Population By Country, 2021</h2>
		<svg bind:this={globe} {width} {height}>
			<Glow />
			<circle
				role="button"
				filter="url('#glow')"
				cx={width / 2}
				cy={height / 2}
				r={width / 2}
				fill="#ececec"
				on:click={() => {
					toolTipData = null;
				}}
				on:keyup={() => {
					toolTipData = null;
				}}
			/>

			{#each countries as country}
				<path
					role="button"
					d={path(country)}
					fill={colorScale(country?.population || 0)}
					stroke="black"
					on:click={() => {
						toolTipData = country;
					}}
					on:keyup={() => {
						toolTipData = country;
					}}
					on:focus={() => {
						toolTipData = country;
					}}
					on:focus={() => {
						toolTipData = country;
					}}
					tabindex="0"
				/>
			{/each}

			<path d={path(borders)} fill="none" stroke="#8d8d8d" />

			{#if toolTipData}
				{#key toolTipData.id}
					<path d={path(toolTipData)} fill="transparent" stroke="yellow" stroke-width="2" in:draw />
				{/key}
			{/if}
		</svg>
		<ToolTip data={toolTipData} />
		<Legend {colorScale} data={toolTipData} />
	</div>
</section>

<style>
	.chart-container {
		max-width: 487px;
		margin: 0 auto;
	}

	svg {
		overflow: visible;
	}

	.dragging {
		cursor: grab;
	}

	path {
		cursor: pointer;
	}
	path:focus {
		outline: none;
	}

	h1,
	h2 {
		color: white;
		text-align: center;
	}

	h1 {
		font-size: 1.75rem;
		font-weight: 600;
		margin-bottom: 0.35rem;
	}

	h2 {
		font-size: 1.25rem;
		font-weight: 200;
		margin-bottom: 1rem;
	}
</style>
