<script>
    import Square from "./Square.svelte";
    
</script>

<main>
    {#each Array(9) as _}
        <Square/>
    {/each}
</main>

<style>
    main{
        width: 156px;
        display: flex;
        flex-wrap: wrap;
        border: 2px solid black;
    }
</style>