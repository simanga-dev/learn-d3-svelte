<script>
    export let data;
    export let colorScale;
    export let width;
    import { fly, fade } from "svelte/transition";

    let tooltipWidth;

    const xNudge = 5;
    const yNudge = 5;

    // If the x position + the tooltip width exceeds the chart width, offset backward to prevent overflow
    $: xPosition =
        data.x + tooltipWidth + xNudge > width
            ? data.x - tooltipWidth - xNudge
            : data.x + xNudge;
    $: yPosition = data.y + yNudge;
</script>

<div
    class="tooltip"
    in:fly={{ y: 10, duration: 200, delay: 200 }}
    out:fade
    style="position: absolute; top: {yPosition}px; left: {xPosition}px"
    bind:clientWidth={tooltipWidth}
>
    <!-- Country name -->
    <h1>
        {data.country}
    </h1>
    <div class="info">
        <span class="score">
            {data.happiness}/10
        </span>
        <span
            class="continent"
            style="background: {colorScale(data.continent)}"
        >
            {data.continent}
        </span>
    </div>
    <span class="bar background" />
    <span
        class="bar foreground"
        style="background: {colorScale(data.continent)};
        width: {data.happiness * 10}%
        "
    />
</div>

<style>
    .tooltip {
        position: absolute;
        background: white;
        box-shadow: rgba(0, 0, 0, 0.15) 2px 3px 8px; /* Gives a nice 3d effect */
        padding: 8px 6px; /* Adds space around content within tooltip */
        border-radius: 3px; /* Rounds corners */
        pointer-events: none; /* Prevents tooltip from blocking mouse events */
        transition: top 300ms ease, left 300ms ease;
    }
    .info {
        display: flex;
        justify-content: space-between;
        column-gap: 8px;
    }

    .continent {
        font-size: 0.65rem;
        padding: 3px 4px 2px 4px;
        border-radius: 3px;
        text-transform: uppercase;
        white-space: nowrap; /* Prevents line breaks */
    }
    h1 {
        margin: 0;
        font-size: 1rem;
        font-weight: 500;
        margin-bottom: 3px;
    }

    .bar {
        position: absolute;
        bottom: 0; /* Forces to bottom of tooltip */
        left: 0; /* Forces to left of tooltip */
        height: 3px;
        width: 100%;
    }
    .bar.background {
        background: #ccc;
    }
</style>
