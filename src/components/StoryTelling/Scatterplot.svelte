<script>
    import data from "$data/data.js";
    import { scaleLinear } from "d3-scale";
    import AxisX from "./AxisX.svelte";
    import AxisY from "./AxisY.svelte";
    import Tooltip from "./Tooltip.svelte";
    import Scrolly from "./Scrolly.svelte";

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

    let currentStep;

    let initialData = data.sort((a, b) => a.grade - b.grade);
    let renderData = initialData;

    $: X_MIDPOINT = (xScale.domain()[0] + xScale.domain()[1]) / 2;
    $: Y_MIDPOINT = (yScale.domain()[0] + yScale.domain()[1]) / 2;

    $: {
        if (currentStep == 0) {
            // set both
            renderData = initialData.map((d) => ({
                ...d,
                grade: X_MIDPOINT,
                hours: Y_MIDPOINT,
            }));
        } else if (currentStep == 1) {
            renderData = initialData.map((d) => ({
                ...d,
                hours: Y_MIDPOINT,
            }));
        } else if (currentStep == 2) {
            renderData = initialData;
            hoveredData = renderData[13];
        }
    }
</script>

<section>
    <div class="sticky">
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
                    {#if currentStep > 0}
                        <AxisX
                            {xScale}
                            width={innerWidth}
                            height={innerHeight}
                        />
                    {/if}

                    {#if currentStep > 1}
                        <AxisY {yScale} width={innerWidth} />
                    {/if}
                    {#each renderData as d}
                        <circle
                            role="button"
                            cx={xScale(d.grade)}
                            cy={yScale(d.hours)}
                            r={hoveredData == d ? radius * 2 : radius}
                            opacity={hoveredData
                                ? hoveredData == d
                                    ? 1
                                    : 0.45
                                : 0.85}
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
                <Tooltip
                    {xScale}
                    {yScale}
                    width={innerWidth}
                    data={hoveredData}
                />
            {/if}
        </div>
    </div>

    <div class="steps">
        <Scrolly bind:value={currentStep}>
            {#each ["Hello", "Scrollytelling", "Hello word"] as text, i}
                <div class:active={currentStep === i} class="step">
                    <div class="step-content">
                        <p>{text}</p>
                    </div>
                </div>
            {/each}
        </Scrolly>
    </div>
</section>

<style>
    section {
        position: relative;
    }

    .sticky {
        z-index: 0;
        position: sticky;
        top: 0;
    }

    .step {
        height: 90vh;
        opacity: 0.3;
        transition: opacity 300ms ease;

        display: flex;
        justify-content: center;
        place-items: center;
    }
    .step-content {
        background: white;
        border: 1px solid black;
        padding: 0.75rem 1rem;
        border-radius: 3px;
    }

    .step.active {
        opacity: 1;
    }

    :global(.tick text, .axis-title) {
        fill: #8f8f8f;
        font-weight: 400;
        font-size: 14px;
    }

    .chart-container {
        position: relative;
    }
    circle {
        transition: r 300ms ease, opacity 500ms ease,
            cx 500ms cubic-bezier(0.76, 0, 0.24, 1),
            cy 500ms cubic-bezier(0.76, 0, 0.24, 1);
        /* cx 300ms ease, opacity 500ms ease, */
        /* cy 300ms ease, opacity 500ms ease; */
        cursor: pointer;
    }
    h1 {
        font-size: 1.3rem;
        text-transform: capitalize;
        margin-bottom: 0.8rem;
    }
    .steps {
        z-index: 999;
    }
</style>
