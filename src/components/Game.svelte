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
    let hints = null

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
        btnSolve.style.border = '2px solid #a7c957'
        btnSolve.style.backgroundColor = '#55dde0'
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
        btnSolve.style.border = '2px solid #55dde0'
        btnSolve.style.backgroundColor = '#a7c957'
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
        btnSolve.style.border = '2px solid #e76f51'
        btnSolve.style.backgroundColor = '#55dde0'
      }else{
        tableShow.style.display = 'none'
        btnSolve.textContent = 'Show lefts'
        btnSolve.style.border = '2px solid #55dde0'
        btnSolve.style.backgroundColor = '#e76f51'
      }
    }

    function getNumLeft(event){
      numLeft = event.detail
      numLeftCopy = JSON.parse(JSON.stringify(numLeft))
    }
    function sendHintsStatus(){
      isHints = document.getElementById('hints').checked
    }
    function getHints(event){
      hints = event.detail
    }
    function setErase(){
      if(hints != null){
        for(let i = 0; i<hints.length; i++){
          const row = hints[i]
          for(let j=0; j<row.length; j++){
            const currentCell = document.getElementById(`${i}_${j}`)

            if(typeof row[j] == 'string' && currentCell.style.backgroundColor == 'cornflowerblue'){
              hints[i][j] = null
              currentCell.style.backgroundColor = ''
            }
          }
        }
      }
    }

</script>

<main>
  <nav>
    <h1>Sudoku Svelte</h1>

    <Link to='/'><button id="back--btn" style="vertical-align:middle"><span>Back </span></button></Link>
  </nav>
    

  {#if !isSolvable}
      <div id="unsolvable">
          <h1>This sudoku is unsolvable!</h1>
      </div>
  {/if}


  <div id="content">

    <div id="manage">
        <label for="hints" style="color: #55dde0; font-size: 20px; margin-top:-6px">Hints</label>
        <input type="checkbox" name="hints" id="hints" on:change={sendHintsStatus}>
        <button class="manage--btn" id="erase" on:click={setErase}>Erase hints</button>

        <button class="manage--btn" id="solve" on:click={solveByClick}>Solve sudoku</button>
        <button class="manage--btn" id="showLefts" on:click={showLefts}>Show lefts</button>

        <button class="manage--btn" id="print--board" on:click={()=>{window.print()}}>Print board</button>
        
    </div>
    

    <div id="sudoku">
      <div id="board">
        <Board {sudoku} {solvedSudoku} {isSolvedByClick} {isHints} {hints} on:numLeft={getNumLeft} on:hints={getHints}/>
      </div>

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
    nav{
      width: 100vw;
      height: 70px;
      background-color: #55dde0;
      position: fixed;
      top: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 200;
    }
    nav>h1{
      font-size: 30px;
      color: #084c61;
    }
    #erase{
      background-color: #f4a261;
    }
    #solve{
      background-color: #a7c957;
    }
    #showLefts{
      background-color: #e76f51;
    }
    #print--board{
      background-color: #2a9d8f;
    }
    #back--btn{
      position: absolute;
      top: 14px;
      right: 20px;
      z-index: 100;
      margin: 0;
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
      padding: 15px;
      border: 1px solid black;
      margin-top: 50px;
      margin-bottom: 50px;
    }
    #manage>*{
      margin: 0;
    }
    .manage--btn{
      width: 120px;
      border: 2px solid #55dde0;
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
      top: 178px;
      right: 17vw;
    }
    table{
      width: 100%;
      height: 100%;
      border-collapse: collapse;
      border: 2px solid #774936;
      text-align: center;
      background-color: #c38e70;
      color: white;
    }
    td, th{
      border: 1px solid #774936;
    }
    #unsolvable{
      width: 100%;
      height: 100%;
      position: fixed;
      top: 70px;
      background-color: grey;
      display: flex;
      justify-content: center;
      align-items: center;
      opacity: 0.8;
    }
    .prefilled {
    background-color: #c08552 !important;
  }
  @media print{
    main *:not(#content):not(#sudoku):not(#board):not(#board *){
      visibility: hidden;
    }
  }

</style>