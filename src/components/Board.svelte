<script>
    import Nine from "./Nine.svelte";
    
</script>

<main>
    {#each Array(9) as _}
        <Nine/>
    {/each}
</main>

<style>
    main{
        width: 480px;
        display: flex;
        flex-wrap: wrap;
        border: 2px solid black;
    }
</style>