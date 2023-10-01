<script>
    export let colorScale;
</script>

<div class="legend">
    {#each colorScale.domain() as domain}
        <span style="background-color: {colorScale(domain)}" />
        <p>{domain}</p>
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
    }
</style>
