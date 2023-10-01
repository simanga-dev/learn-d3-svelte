<script>
    export let yScale;
    export let groupByContinet;
    import { fade } from "svelte/transition";

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
