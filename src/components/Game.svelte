<script>
    import Board from "./Board.svelte";
    let decodedCookie = decodeURIComponent(document.cookie);
    let sudoku = decodedCookie.substring(7)
    sudoku = JSON.parse(sudoku)
    console.log(sudoku);

    function isSolvable(event){
        console.log(event.detail);
        console.log('lol');
    }

</script>

<main>
    <h2>Sudoku Svelte</h2>

    <Board {sudoku} on:isSolvable={isSolvable}/>
</main>

<style>
    main{
        display: flex;
        flex-direction: column;
        align-items: center;
    }
</style>