<script lang="ts">
	import dataset from '$lib/data/boston_neighborhoods.json';
	import type { Topology, GeometryObject } from 'topojson-specification';
	import za_data from './assets/za-all.json';
	import { geoAlbers, geoEquirectangular, geoPath } from 'd3-geo';

	import * as topojson from 'topojson-client';

	let provinces = topojson.feature(
		za_data as unknown as Topology,
		za_data.objects.prov as GeometryObject
	).features;

	let borders = topojson.mesh(
		za_data as unknown as Topology,
		za_data.objects.prov as GeometryObject,
		(a: GeometryObject, b: GeometryObject) => a !== b
	);

	console.log(provinces);

	let width = 400,
		height = 600;

	const margin = {
		top: 15,
		right: 15,
		bottom: 15,
		left: 15
	};

	$: innerWidth = width >= height ? height : width - margin.left - margin.right;
	let innerHeight = height - margin.top - margin.bottom;

	$: projection = geoAlbers()
		.scale(190000)
		.rotate([71.057, 0])
		.center([0, 42.313])
		.translate([width / 2, height / 2]);

	$: path = geoPath(projection);

	$: za_projection = geoEquirectangular()
		.scale(3000)
		.rotate([-24, 0])
		.center([0, -30])
		.translate([width / 2, height / 2]);

	$: za_path = geoPath(za_projection);
</script>

<a class="text-blue-300" href="/">go back to charts</a>

<section>
	<div class="chart-container" bind:clientWidth={width}>
		<svg {width} {height}>
			<g transform="translate({margin.left}, {margin.top})">
				{#each dataset.features as feature}
					<path stroke="black" fill="none" d={path(feature)} />
				{/each}
			</g>
		</svg>

		<svg {width} {height}>
			<g transform="translate({margin.left}, {margin.top})">
				{#each provinces as feature}
					<path stroke="black" fill="none" d={za_path(feature)} />
				{/each}

				<path d={za_path(borders)} fill="none" stroke="#8d8d8d" />
			</g>
		</svg>
	</div>
</section>
