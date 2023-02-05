<script>
    import Board from "./Board.svelte";
    import {Link} from "svelte-routing";

    let decodedCookie = decodeURIComponent(document.cookie);
    let sudoku = decodedCookie.substring(7)
    sudoku = JSON.parse(sudoku)
    let solvedSudoku = JSON.parse(JSON.stringify(sudoku))
    let isSolvable = true

    console.log(sudoku);
    console.log(solvedSudoku);

    
    
    function isValid(board, row, col, k) {
      for (let i = 0; i < 9; i++) {
          const m = 3 * Math.floor(row / 3) + Math.floor(i / 3);
          const n = 3 * Math.floor(col / 3) + i % 3;
          if (board[row][i] == k || board[i][col] == k || board[m][n] == k) {
            return false;
          }
      }
      return true;
    }

    sudokuSolver(solvedSudoku)
    function sudokuSolver(data) {
      for (let i = 0; i < 9; i++) {
        for (let j = 0; j < 9; j++) {
          if (data[i][j] == null) {
            for (let k = 1; k <= 9; k++) {
              if (isValid(data, i, j, k)) {
                data[i][j] = k;
                if (sudokuSolver(data)) {
                return true;
                } else {
                data[i][j] = null;
                }
              }
            }
            return false;
          }
        }
      }
    return true;
    }

    if(solvedSudoku.some(row => row.includes(null))){
        isSolvable = false
        console.log('nie');
    }

</script>

<main >
    <Link to='/'><span id="back">Back</span></Link>

    <h2>Sudoku Svelte</h2>

    {#if !isSolvable}
        <div id="unsolvable">
            <h1>Nie da się rozwiązać tego sudoku!</h1>
        </div>
    {/if}
    

    <Board {sudoku} {solvedSudoku}/>
</main>

<style>
    main{
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    #back{
        position: fixed;
        top: 10vh;
        right: 10vw;
        z-index: 10;
    }
    #unsolvable{
        width: 90vw;
        height: 90vh;
        position: fixed;
        top: 5vh;
        left: 5vw;
        background-color: grey;
        display: flex;
        justify-content: center;
        align-items: center;
        opacity: 0.8;
    }
</style>