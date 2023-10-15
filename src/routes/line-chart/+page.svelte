<script lang="ts">
	import dataset from './nyc_weather_data.json';
	import { timeParse } from 'd3-time-format';
	import { extent } from 'd3-array';
	import { scaleLinear, scaleTime } from 'd3-scale';
	import { line } from 'd3-shape';

	let width = 400,
		height = 400;

	let margin = {
		top: 15,
		right: 15,
		bottom: 40,
		left: 60
	};

	let dateParser = timeParse('%Y-%m-%d');

	$: innerWidth = width - margin.left - margin.right;
	let innerHeight = height - margin.top - margin.bottom;

	const yAccessor = (d: (typeof dataset)[0]) => d.temperatureMax;

	const xAccessor = (d: (typeof dataset)[0]) => dateParser(d.date);

	const yExtent = extent(dataset, yAccessor);
	const yScale = scaleLinear()
		.domain(yExtent[0] ? yExtent : [0, 100])
		.range([innerHeight, 0]);

	const xExtent = extent(dataset, xAccessor);

	$: xScale = scaleTime()
		.domain(xExtent[0] ? xExtent : [new Date(), new Date()])
		.range([0, innerWidth]);

	$: lineGeneratore = line<(typeof dataset)[0]>()
		.x((d) => xScale(xAccessor(d) || new Date()))
		.y((d) => yScale(yAccessor(d)));

	let freezingTemp = yScale(32);

	// $: console.log(lineGeneratore(dataset));
	// $: console.log(xExtent);
	// $: console.log(xScale(dataset[0].date));
	// $: console.log(xScale(xExtent[1]));
</script>

<a class="text-blue-300" href="/">go back to charts</a>
<section>
	<div class="chart-conatiner" bind:clientWidth={width}>
		<svg class="h" {width} {height}>
			<g transform="translate({margin.left}, {margin.top})">
				<rect x="0" y={freezingTemp} width={innerWidth} height={innerHeight} fill="pink" />
				<path fill="none" stroke-width="2" stroke="#1f3957"  d={lineGeneratore(dataset)} />
			</g>
		</svg>
	</div>
</section>
