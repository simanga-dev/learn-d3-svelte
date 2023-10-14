<script lang="ts">
	import data from './data';
	import { forceSimulation, forceX, forceY, forceCollide, type Simulation } from 'd3-force';
	import { scaleLinear, scaleOrdinal, scaleBand, scaleSqrt } from 'd3-scale';
	import { extent, mean, rollups } from 'd3-array';
	import { fade } from 'svelte/transition';

	type Node = {
		index: number;
		country: string;
		happiness: number;
		continent: string;
		x: number;
		y: number;
	};

	let width = 400,
		height = 400;

	const margin = {
		top: 15,
		right: 15,
		bottom: 15,
		left: 15
	};

	console.log(width);

	const continents = rollups(
		data,
		(v) => mean(v, (d) => d.happiness),
		(d) => d.continent
	) // Group data by continent and return the group-wide mean
		.sort((a, b) => {
			if (a[1] && b[1]) return a[1] - b[1];
			return 0;
		}) // Sort according to value
		.map((d) => d[0]); // Grab the continent name

	let minMax = extent(data, (d) => d.happiness);
	$: radiusScale = scaleSqrt()
		.domain(minMax[1] ? minMax : [2, 8])
		.range(width < 568 ? [2, 6] : [3, 8]);

	const colorRange = ['#dda0dd', '#fe7f2d', '#fcca46', '#a1c181', '#619b8a', '#eae2b7'];
	const colorScale = scaleOrdinal<string>().domain(continents).range(colorRange);

	$: innerWidth = width - margin.left - margin.right;
	let innerHeight = height - margin.top - margin.bottom;

	$: xScale = scaleLinear().domain([1, 9]).range([0, innerWidth]);

	let yScale = scaleBand().domain(continents).range([innerHeight, 0]).paddingOuter(0.5);

	let groupByContinet = false;

	const simulation = forceSimulation(data.map((d, i) => ({ ...d, index: i, x: 0, y: 0 })));
	$: {
		simulation
			.force(
				'x',
				forceX<Node>()
					.x((d) => xScale(d.happiness))
					.strength(0.8)
			)
			.force(
				'y',
				forceY<Node>()
					.y((d) => {
						let ys = yScale(d.continent);
						if (ys) return groupByContinet ? ys : innerHeight / 2;
						return 0;
					})
					.strength(0.2)
			)
			.force(
				'collide',
				forceCollide<Node>().radius((d) => radiusScale(d.happiness))
			)
			.alpha(0.3)
			.alphaDecay(0.0005)
			.restart();
	}

	let nodes: Node[] | [] = [];

	simulation.on('tick', () => {
		nodes = simulation.nodes();
	});
</script>

<section>
	<div bind:clientWidth={width}>
		<svg {width} {height}>
			<g transform="translate({margin.left}, {margin.top})">
				{#each nodes as node, index}
					<circle
						in:fade={{ delay: 500 + index * 10 }}
						role="button"
						cx={node.x}
						cy={node.y}
						r={radiusScale(node.happiness)}
						fill={colorScale(node.continent)}
					/>
				{/each}
			</g>
		</svg>
	</div>
</section>

<!-- fill={colorScale(node.continent)} -->
<!-- 	// let hovered; -->
<!-- 	// let hoveredContinent; -->
<!-- </script> -->

<!-- <h1>Hello</h1> -->
<!---->
<!-- <h1>The happiness continent in the world</h1> -->
