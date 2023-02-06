<script>
    import Board from "./Board.svelte";
    import RestNum from "./RestNum.svelte";
    import {Link} from "svelte-routing";

    let decodedCookie = decodeURIComponent(document.cookie);
    let sudoku = decodedCookie.substring(7)
    sudoku = JSON.parse(sudoku)
    const sudokuCopy = JSON.parse(JSON.stringify(sudoku))
    let solvedSudoku = JSON.parse(JSON.stringify(sudoku))
    let isSolvable = true
    let numLeft = {}

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
    }

    function solveByClick(){
      const btnSolve = document.getElementById("solve")

      if(!solvedSudoku.equals(sudoku)){
        btnSolve.textContent = 'Reset'
        sudoku = JSON.parse(JSON.stringify(solvedSudoku))

        for(let i = 0; i<sudoku.length; i++){
          const row = sudoku[i]
          for(let j=0; j<row.length; j++){
            const el = document.getElementById(`${i}_${j}`)
            if(el.style.backgroundColor == 'white'){
              console.log(el.style.backgroundColor);
              el.classList.add('prefilled')
            }
          }
        }
      }else{
        btnSolve.textContent = 'Solve sudoku'
        sudoku = JSON.parse(JSON.stringify(sudokuCopy))
      }
    }

    function getNumLeft(event){
      numLeft = event.detail
    }

</script>

<main >
    <h2>Sudoku Svelte</h2>

    <Link to='/'><span id="back">Back</span></Link>

    {#if !isSolvable}
        <div id="unsolvable">
            <h1>That sudoku is unsolvable!</h1>
        </div>
    {/if}


    <div id="content">

      <button id="solve" on:click={solveByClick}>Solve sudoku</button>

      <div id="sudoku">
        <Board {sudoku} {solvedSudoku} on:numLeft={getNumLeft}/>

        <RestNum {numLeft}/>
      </div>
      

    </div>

    
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
    #content{
      width: 100%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 10px;
    }
    #sudoku{
      display: flex;
      align-items: center;
      gap: 20px;
    }
    #solve{
      width: 100px;
      height: 40px;
      font-size: 14px;
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
    .prefilled {
    background-color: beige !important;
  }
</style>