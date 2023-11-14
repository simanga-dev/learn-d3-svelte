<script lang="ts">
	import dataset from '$lib/data/weather-data.json';
	import { group, merge } from 'd3-array';
	import { scaleOrdinal } from 'd3-scale';
	import { pie, arc } from 'd3-shape';
	import { interpolateLab } from 'd3-interpolate';
	console.log('Thank you god for this figt');

	const iconAccessor = (d: (typeof dataset)[0]) => d.icon;

	const datasetByIcon = Array.from(group(dataset, iconAccessor)).sort(
		(a, b) => b[1].length - a[1].length
	);

	const combinedDatasetByIcon = [
		...datasetByIcon.slice(0, 4),
		['other', merge(datasetByIcon.slice(4).map((d) => d[1]))]
	];

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

	const arcGenerator = pie()
		.padAngle(0.01)
		.value(([key, values]) => values.length);

	const arcs = arcGenerator(combinedDatasetByIcon);

	const interpolateWithSteps = (numberOfSteps: number) =>
		new Array(numberOfSteps).fill(null).map((d, i) => i / (numberOfSteps - 1));

	const colorScale = scaleOrdinal()
		.domain(arcs.sort((a, b) => a.data[1].length - b.data[1].length).map((d) => d.data[0]))
		.range(interpolateWithSteps(datasetByIcon.length).map(interpolateLab('#f3a683', '#3dc1d3')));

	$: radius = innerWidth / 2;

	$: arcPath = arc()
		.innerRadius(radius * 0.7) // set to 0 for a pie chart
		.outerRadius(radius);

	console.log(arcs);
</script>

<a class="text-blue-900" href="/">go back to charts</a>

<section>
	<div class="relative" bind:clientWidth={width}>
		<svg {width} {height}>
			<g transform="translate({margin.left}, {margin.top})">
				<g
					transform="translate({innerWidth >= innerHeight
						? width / 2
						: innerWidth / 2}, {innerHeight / 2})"
				>
					{#each arcs as item}
						<path
							fill={item.data[0] === 'other' ? '#ddd' : colorScale(item.data)}
							d={arcPath(item)}
						/>
						<g transform="translate({arcPath.centroid(item)})">
							<text> {item.value}</text>
						</g>
					{/each}
				</g>
			</g>
		</svg>
	</div>
</section>
