<script>
    import data from "$data/data_m3.js";
    import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";
    import { scaleLinear, scaleOrdinal, scaleBand, scaleSqrt } from "d3-scale";
    import { extent, mean, rollups } from "d3-array";
    import AxisX from "./AxisX.svelte";
    import AxisY from "./AxisY.svelte";
    import Legend from "./Legend.svelte";

    let width = 400,
        height = 600;

    const margin = {
        top: 20,
        right: 20,
        bottom: 20,
        left: 20,
    };

    // Generate the average for each continent, so that we can sort according to that
    const continents = rollups(
        data,
        (v) => mean(v, (d) => d.happiness),
        (d) => d.continent
    ) // Group data by continent and return the group-wide mean
        .sort((a, b) => a[1] - b[1]) // Sort according to value
        .map((d) => d[0]); // Grab the continent name

    $: radiusScale = scaleSqrt()
        .domain(extent(data, (d) => d.happiness))
        .range(width < 568 ? [2, 6] : [3, 8]);

    const colorRange = [
        "#dda0dd",
        "#fe7f2d",
        "#fcca46",
        "#a1c181",
        "#619b8a",
        "#eae2b7",
    ];
    const colorScale = scaleOrdinal().domain(continents).range(colorRange);

    $: innerWidth = width - margin.left - margin.right;
    let innerHeight = height - margin.top - margin.bottom;

    $: xScale = scaleLinear()
        // .domain(extent(data, (d) => d.happiness))
        .domain([1, 9])
        .range([0, innerWidth]);

    let yScale = scaleBand()
        .domain(continents)
        .range([innerHeight, 0])
        .paddingOuter(0.5);

    $: simulation = forceSimulation(data)
        .force(
            "x",
            forceX()
                .x((d) => xScale(d.happiness))
                .strength(0.8)
        )
        .force(
            "y",
            forceY()
                .y((d) => yScale(d.continent))
                .strength(0.2)
        )
        .force(
            "collide",
            forceCollide().radius((d) => radiusScale(d.happiness))
        );

    $: nodes = simulation.nodes();
</script>

<h1>The happiness continent in the world</h1>

<div class="chart-container" bind:clientWidth={width}>
    <Legend {colorScale} />
    <svg {width} {height}>
        <g transform="translate({margin.left}, {margin.top})">
            <AxisX {xScale} height={innerHeight} width={innerWidth} />
            <AxisY {yScale} />
            {#each nodes as node}
                <circle
                    cx={node.x}
                    cy={node.y}
                    r={radiusScale(node.happiness)}
                    fill={colorScale(node.continent)}
                    stroke="gray"
                />
            {/each}
        </g>
    </svg>
</div>

<style>
    svg {
        /* background: gray */
    }
</style>
