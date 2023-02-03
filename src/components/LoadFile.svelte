<script>
    import {Link} from "svelte-routing";

</script>

<div id="container">
    <form action="">
        <h2>Load data for Sudoku:</h2>
        <input type="file" name="data" id="data">
        <button><Link to='game'>Load</Link></button>
    </form>
</div>

<style>
    form{
        width: 55vw;
        height: 40vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-evenly;
        background-color: azure;
        border: 1px solid black;
    }
    #data{
        width: 250px;
    }
</style>