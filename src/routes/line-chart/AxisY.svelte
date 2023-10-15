<script lang="ts">
	import type { ScaleLinear } from 'd3-scale';

	export let yScale: ScaleLinear<number, number, never>;
    export let height: number;
	let yTicks = yScale.ticks(8);
</script>

<g class="axis y">
    <line x1="0" y1="0" y2="{height}" x2="0" stroke="#8f8f8f" stroke-width="1" />
{#each yTicks as tick}
	<g class="tick  "  transform="translate(0,{yScale(tick)})"  >
		<line x1="-5" y1="0" x2="0" y2="0" stroke="#8f8f8f" />
		<text x="-15" text-anchor="middle" y="5"> {tick} </text>
	</g>
{/each}

</g>
