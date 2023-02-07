<script>
    import Board from "./Board.svelte";
    import {Link} from "svelte-routing";

    let decodedCookie = decodeURIComponent(document.cookie);
    let sudoku = decodedCookie.substring(7)
    sudoku = JSON.parse(sudoku)
    const sudokuCopy = JSON.parse(JSON.stringify(sudoku))
    let solvedSudoku = JSON.parse(JSON.stringify(sudoku))
    let isSolvable = true
    let isSolvedByClick = false
    let numLeft = {}
    let numLeftCopy = {}
    let isHints = false

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
        isSolvedByClick = true
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

        for(let key in numLeft){
          numLeft[key] = 0
        }
      }else{
        isSolvedByClick = false
        btnSolve.textContent = 'Solve sudoku'
        sudoku = JSON.parse(JSON.stringify(sudokuCopy))
        // console.log(numLeftCopy);
        numLeft = JSON.parse(JSON.stringify(numLeftCopy))
      }
    }

    function showLefts(){
      const btnSolve = document.getElementById("showLefts")
      const tableShow = document.getElementById("numLeft")

      if(tableShow.style.display == 'none' || tableShow.style.display == ''){
        tableShow.style.display = 'block'
        btnSolve.textContent = 'Hide lefts'
      }else{
        tableShow.style.display = 'none'
        btnSolve.textContent = 'Show lefts'
      }
    }

    function getNumLeft(event){
      numLeft = event.detail
      numLeftCopy = JSON.parse(JSON.stringify(numLeft))
    }
    function sendHintsStatus(){
      isHints = document.getElementById('hints').checked
      // console.log(isHints);
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

      <div id="manage">
          <label for="hints">Hints</label>
          <input type="checkbox" name="hints" id="hints" on:change={sendHintsStatus}>

          <button id="solve" on:click={solveByClick}>Solve sudoku</button>
          <button id="showLefts" on:click={showLefts}>Show lefts</button>
          
      </div>
     

      <div id="sudoku">
        <Board {sudoku} {solvedSudoku} {isSolvedByClick} {isHints} on:numLeft={getNumLeft}/>

        <div id="numLeft">
          <table>
            <tr>
              <th>Value</th>
              <th>Lefts</th>
            </tr>
            <tr>
              <td>1</td>
              <td>{numLeft[1]}</td>
            </tr>
            <tr>
              <td>2</td>
              <td>{numLeft[2]}</td>
            </tr>
            <tr>
              <td>3</td>
              <td>{numLeft[3]}</td>
            </tr>
            <tr>
              <td>4</td>
              <td>{numLeft[4]}</td>
            </tr>
            <tr>
              <td>5</td>
              <td>{numLeft[5]}</td>
            </tr>
            <tr>
              <td>6</td>
              <td>{numLeft[6]}</td>
            </tr>
            <tr>
              <td>7</td>
              <td>{numLeft[7]}</td>
            </tr>
            <tr>
              <td>8</td>
              <td>{numLeft[8]}</td>
            </tr>
            <tr>
              <td>9</td>
              <td>{numLeft[9]}</td>
            </tr>
          </table>
        </div>

      </div>

    </div>

    
</main>

<style>
    main{
      display: flex;
      flex-direction: column;
      align-items: center;
      position: relative;
    }
    #back{
      position: fixed;
      top: 10vh;
      right: 10vw;
      z-index: 100;
    }
    #content{
      width: 100%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 10px;
    }
    #manage{
      width: 100vw;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 20px;
      padding: 10px;
      border: 1px solid black;
    }
    #manage>*{
      margin: 0;
    }
    
    #sudoku{
      display: flex;
      align-items: center;
      gap: 20px;
    }
    #numLeft{
      display: none;
      width: 20vw;
      max-width: 200px;
      height: 253px;
      background-color: aliceblue;
      position: absolute;
      top: 100px;
      right: 5vw;
    }
    table{
      width: 100%;
      height: 100%;
      border-collapse: collapse;
      border: 1px solid black;
      text-align: center;
    }
    td, th{
      border: 1px solid black;
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