<script>
    import { Link } from "svelte-routing";
</script>

<main>
    <h2>No data send!</h2>
    <Link to='/'><button id="back--btn" style="vertical-align:middle"><span>Back </span></button></Link>
</main>

<style>
    main{
        min-width: 300px;
        width: 30vw;
        height: 20vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-evenly;
        background-color: azure;
        border: 1px solid black;
        box-shadow: 0 1px 20px 1px black;
    }
    
</style>