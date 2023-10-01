<script>
    import data from "$data/data.js";
    import { scaleLinear } from "d3-scale";
    import AxisX from "./AxisX.svelte";
    import AxisY from "./AxisY.svelte";
    import Tooltip from "./Tooltip.svelte";

    const margin = {
        top: 20,
        right: 20,
        bottom: 20,
        left: 15,
    };

    let width = 400;
    let height = 400;
    $: innerWidth = width - margin.left - margin.right;
    let innerHeight = height - margin.top - margin.bottom;
    $: xScale = scaleLinear().domain([0, 100]).range([0, innerWidth]);
    let yScale = scaleLinear().domain([0, 60]).range([innerHeight, 0]);

    let hoveredData;
    let radius = 10;
</script>

<h1>student who study longer do well in exam</h1>
<div class="chart-container" bind:clientWidth={width}>
    <svg
        {width}
        {height}
        on:mouseleave={() => {
            hoveredData = "";
        }}
    >
        <g transform="translate({margin.left}, {margin.top})">
            <AxisX {xScale} width={innerWidth} height={innerHeight} />
            <AxisY {yScale} width={innerWidth} />
            {#each data.sort((a, b) => a.grade - b.grade) as d}
                <circle
                    role="button"
                    cx={xScale(d.grade)}
                    cy={yScale(d.hours)}
                    r={hoveredData == d ? radius * 2 : radius}
                    opacity={hoveredData ? (hoveredData == d ? 1 : 0.45) : 0.85}
                    fill="purple"
                    stroke="black"
                    stroke-width="1"
                    on:mouseover={() => {
                        hoveredData = d;
                    }}
                    on:focus={() => {
                        hoveredData = d;
                    }}
                    tabindex="0"
                />
            {/each}
        </g>
    </svg>

    {#if hoveredData}
        <Tooltip {xScale} {yScale} width={innerWidth} data={hoveredData} />
    {/if}
</div>

<style>
    :global(.tick text, .axis-title) {
        fill: #8f8f8f;
        font-weight: 400;
        font-size: 14px;
    }

    .chart-container {
        position: relative;
    }
    circle {
        transition: r 300ms ease, opacity 500ms ease;
        cursor: pointer;
    }
    h1 {
        font-size: 1.3rem;
        text-transform: capitalize;
        margin-bottom: 0.8rem;
    }
</style>
