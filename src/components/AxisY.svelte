<script>
    import { index } from "d3-array";

    export let yScale;
    export let width;
    let yTicks = yScale.ticks(4);

    console.log(yTicks);
</script>

{#each yTicks as tick, index}
    <g class="tick" transform="translate(0,{yScale(tick)})">
        <line
            x1="0"
            y1="0"
            x2={width}
            y2="0"
            stroke={index == 0 ? "#8f8f8f" : "#e5e7eb"}
        />

        <text x="0" y="-6"
            >{tick}{index === yTicks.length - 1 ? " hours studied" : ""}</text
        >
    </g>
{/each}
