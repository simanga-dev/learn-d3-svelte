<script lang="ts">
    export let data;
    import { format } from "d3-format";
    import { fade } from "svelte/transition";

    const suffixFormat = (d) => format(".2s")(d).replace("G", "B");
</script>

{#if data?.country}
    <div transition:fade>
        <h2>{data.country}</h2>
        <h3>population of {suffixFormat(data.population)}</h3>
    </div>
{/if}

<style>
    div {
        position: absolute;
        bottom: 30px;
        right: 0;
        text-align: right;
    }

    h2 {
        font-size: 1.5rem;
        font-weight: 700;
        color: black;
        margin-bottom: 0.25rem;
    }

    h3 {
        font-size: 1.25rem;
        font-weight: 200;
        color: rgba(0, 0, 0, 0.8);
    }
</style>
