<script>
    export let xScale;

    export let width;
    export let height;
    $: ticks = xScale.ticks(4);
</script>

<g class="axis x">
    {#each ticks as tick}
        <g transform="translate({xScale(tick)},0)" class="tick">
            <text x="0" text-anchor="middle" y={height}>{tick} </text>
            <line
                x1="0"
                y1="0"
                x2="0"
                y2={height -12}
                stroke="hsla(215, 15%, 91%, 1)"
            />
        </g>
    {/each}
    <text x={width} y={height} dy="11" text-anchor="end" class="axis-title">
        Happiness,out of 10 &rarr;
    </text>
</g>
