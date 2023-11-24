<script lang="ts">
	import dataset from '$lib/data/boston_neighborhoods.json';
	import { geoAlbers, geoPath } from 'd3-geo';

	console.log(dataset);

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
</script>

<a class="text-blue-300" href="/">go back to charts</a>

<section>
	<div class="chart-container" bind:clientWidth={width}>
		<svg {width} {height}>
			<g transform="translate({margin.left}, {margin.top})">
				{#each dataset.features as feature}
					<path stroke="black" fill="none"  d={path(feature)} />
				{/each}
			</g>
		</svg>
	</div>

	Hello from Geo
</section>
