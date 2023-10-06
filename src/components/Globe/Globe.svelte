<script>
    import world from "$data/world.json";
    import * as topojson from "topojson-client";
    import { geoOrthographic, geoPath, geoCentroid } from "d3-geo";
    import Glow from "./Glow.svelte";
    import ToolTip from "./ToolTip.svelte";
    import Legend from "./Legend.svelte";
    import data from "$data/data_p.json";
    import { scaleLinear } from "d3-scale";
    import { max } from "d3-array";
    import { timer } from "d3-timer";
    import { onMount } from "svelte";
    import { drag } from "d3-drag";
    import { select } from "d3-selection";
    import { spring } from "svelte/motion";
    import { draw } from "svelte/transition";

    // const countries = topojson.feature(world, world.objects.contries).features
    let countries = topojson.feature(world, world.objects.countries).features;
    let borders = topojson.mesh(
        world,
        world.objects.countries,
        (a, b) => a !== b
    );

    let toolTipData;

    countries.forEach((country) => {
        const metadata = data.find((d) => d.id === country.id);
        if (metadata) {
            country.population = metadata.population;
            country.country = metadata.country;
        }
    });

    let xRotation = spring(0, {
        stiffness: 0.08,
        damping: 0.4,
    });

    let yRotation = spring(-30, {
        stiffness: 0.1,
        damping: 0.7,
    });

    let degressPerFrame = 0.5;

    let width = 400;
    $: height = width;

    $: projection = geoOrthographic()
        .scale(width / 2)
        .rotate([$xRotation, $yRotation])
        .translate([width / 2, height / 2]);

    $: path = geoPath(projection);

    const DRAG_SENSENTIVITY = 0.5;
    let dragging = false;

    const colorScale = scaleLinear()
        .domain([0, max(data, (d) => d.population)])
        .range(["#26362e", "#0DCC6C"]);

    const t = timer((elapsed) => {
        if (dragging || toolTipData) return;
        $xRotation += degressPerFrame;
    }, 1);

    let globe;
    onMount(() => {
        const element = select(globe);
        element.call(
            drag()
                .on("drag", (event) => {
                    $xRotation = $xRotation + event.dx * DRAG_SENSENTIVITY;
                    $yRotation = $yRotation - event.dy * DRAG_SENSENTIVITY;
                    dragging = true;
                })
                .on("end", () => {
                    dragging = false;
                })
        );
    });

    $: if (toolTipData) {
        const center = geoCentroid(toolTipData);
        $xRotation = -center[0];
        $yRotation = -center[1];
    }
</script>

<div class="chart-container" bind:clientWidth={width}>
    <h1>The world at glance</h1>
    <h2>Population By Country, 2021</h2>
    <svg bind:this={globe} class:dragging {width} {height}>
        <Glow />
        <circle
            role="button"
            filter="url('#glow')"
            cx={width / 2}
            cy={height / 2}
            r={width / 2}
            fill="#1c1c1c"
            on:click={() => {
                toolTipData = null;
            }}
            on:keyup={() => {
                toolTipData = null;
            }}
        />

        {#each countries as country}
            <path
                role="button"
                d={path(country)}
                fill={colorScale(country?.population || 0)}
                stroke="black"
                on:click={() => {
                    toolTipData = country;
                }}
                on:keyup={() => {
                    toolTipData = country;
                }}
                on:focus={() => {
                    toolTipData = country;
                }}
                on:focus={() => {
                    toolTipData = country;
                }}
                tabindex="0"
            />
        {/each}
        <path d={path(borders)} fill="none" stroke="black" />
        {#if toolTipData}
            {#key toolTipData.id}
                <path
                    d={path(toolTipData)}
                    fill="transparent"
                    stroke="white"
                    stroke-width="2"
                    in:draw
                />
            {/key}
        {/if}
    </svg>
    <ToolTip data={toolTipData} />
    <Legend {colorScale} data={toolTipData} />
</div>

<style>
    .chart-container {
        max-width: 487px;
        margin: 0 auto;
    }

    svg {
        overflow: visible;
    }

    .dragging {
        cursor: grab;
    }

    :global(body) {
        background: rgb(40, 40, 40);
    }
    path {
        cursor: pointer;
    }
    path:focus {
        outline: none;
    }

    h1,
    h2 {
        color: white;
        text-align: center;
    }

    h1 {
        font-size: 1.75rem;
        font-weight: 600;
        margin-bottom: 0.35rem;
    }

    h2 {
        font-size: 1.25rem;
        font-weight: 200;
        margin-bottom: 1rem;
    }
</style>
