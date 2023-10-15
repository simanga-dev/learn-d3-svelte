<script lang="ts">
	import type { ScaleLinear } from 'd3-scale';

	export let height: number;
	export let width: number;

	export let xScale: ScaleLinear<Date, Date, never>;
	let xTicks = xScale.ticks(20);

</script>

<g transform="translate(0, {height})">
	<line x1="0" y1="0" y2="0" x2={width} stroke-width="1" stroke="#8f8f8f" />
	{#each xTicks as tick}
		<g class="tick" transform="translate({xScale(tick)}, 0)">
			<text x="0" y="6" dy="10" dominant-baseline="middle">
				{tick.toLocaleString('default', { month: 'long' })}
			</text>
		</g>
	{/each}

	<text x="0" y="19" dy="10" dominant-baseline="middle"> 2018 </text>
</g>
