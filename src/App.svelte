<script>
    import data from "$data/data.js";
    import { scaleLinear } from "d3-scale";
    import AxisX from "$components/AxisX.svelte";
    import AxisY from "$components/AxisY.svelte";

    const margin = {
        top: 20,
        right: 20,
        bottom: 20,
        left: 0,
    };

    let width = 400;
    let height = 400;
    let innerWidth = width - margin.left - margin.right;
    let innerHeight = height - margin.top - margin.bottom;
    let xScale = scaleLinear().domain([0, 100]).range([0, innerWidth]);
    let yScale = scaleLinear().domain([0, 60]).range([innerHeight, 0]);
</script>

<svg {width} {height}>
    <g transform="translate({margin.left}, {margin.top})">
        <AxisX {xScale} width={innerWidth} height={innerHeight} />
        <AxisY {yScale} width={innerWidth} />
        {#each data as d}
            <circle
                cx={xScale(d.grade)}
                cy={yScale(d.hours)}
                r={10}
                fill="purple"
                stroke="black"
                stroke-width="1"
            />
        {/each}
    </g>
</svg>

<style>
    :global(.tick text, .axis-title) {
        fill: #8f8f8f;
        font-weight: 400;
        font-size: 14px;
    }
</style>
