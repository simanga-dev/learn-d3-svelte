<script lang="ts">
	import type { ScaleBand } from "d3-scale";
    import { fade } from "svelte/transition";


    export let yScale: ScaleBand<string>
    export let groupByContinet: boolean

    const ticks = yScale.domain();
</script>


<g class="ticks">
    {#each ticks as tick, index}
        {#if groupByContinet}
            <g class="tick" in:fade={{ delay: index * 100 }} out:fade>
                <text y={yScale(tick)} x="0">{tick}</text>
            </g>
        {/if}
    {/each}
</g>
