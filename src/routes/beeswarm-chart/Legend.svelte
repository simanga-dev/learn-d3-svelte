<script lang="ts">
	import type { ScaleOrdinal } from "d3-scale";


    export let colorScale: ScaleOrdinal<string, string, never>;
    export let hoveredContinent:  string | null;
    let continents = colorScale.domain();
</script>

<div
    class="legend"
    on:mouseleave={() => {
        hoveredContinent = null;
    }}
>
    {#each continents as continent}
        <p
            on:mouseover={() => {
                hoveredContinent = continent;
            }}
            on:focus={() => {
                hoveredContinent = continent;
            }}
            class:unHoveredContinent={hoveredContinent &&
                hoveredContinent !== continent}
        >
            <span style="background-color: {colorScale(continent)}" />
            {continent}
        </p>
    {/each}
</div>

<style>
    .legend {
        display: flex;
        flex-direction: row;
        justify-content: center;
        flex-wrap: wrap;
        column-gap: 10px;
        row-gap: 5px;
        margin-bottom: 0.25rem; /* Adds a bottom margin */
    }
    span {
        width: 9px;
        height: 9px;
        display: inline-block; /* Makes the span behave like an inline element */
        border-radius: 50%; /* Makes the span a circle */
        border: 1px solid rgba(0, 0, 0, 0.5); /* Adds a slight border */
    }
    p {
        font-size: 0.8rem;
        text-transform: uppercase;
        display: flex;
        align-items: center;
        column-gap: 3px; /* Adds a slight gap between the span and the text */
        cursor: pointer;
        transition: opacity 300ms ease;
    }
    .unHoveredContinent {
        opacity: 0.3;
    }
</style>
