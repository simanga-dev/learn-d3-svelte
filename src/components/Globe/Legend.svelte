<script>
    import { format } from "d3-format";
    import { fade } from "svelte/transition";
    export let colorScale;
    export let data;
    const minColor = colorScale.range()[0];
    const maxColor = colorScale.range()[1];

    const minValue = colorScale.domain()[0];
    const maxValue = colorScale.domain()[1];

    const suffixFormat = (d) => format(".2s")(d).replace("G", "B");

    $: percentMax = (data?.population / maxValue) * 1000;
</script>

<div class="legend">
    <span class="label">{minValue}</span>
    <div
        class="bar"
        style:background="linear-gradient(to right, {minColor} 0%,
        {maxColor} 100%)"
    >
        {#if percentMax}
            <span transition:fade style:left={percentMax + "%"} class="line" />
        {/if}
    </div>
    <span class="label">{suffixFormat(maxValue)}</span>
</div>

<style>
    .line {
        position: absolute;
        top: 0;
        width: 2px;
        height: 15px;
        background: white;
        transition: left 800ms cubic-bezier(1, 0, 0, 1);
    }
    .legend {
        display: flex;
        gap: 6px;
    }
    .bar {
        height: 15px;
        width: 100%;
        background: white;
        position: relative;
    }
    .label {
        color: white;
        font-size: 0.8rem;
        user-select: none;
    }
</style>
