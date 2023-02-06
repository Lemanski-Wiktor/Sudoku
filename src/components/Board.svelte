<script>
  import { Confetti } from "svelte-confetti"
  import { onMount } from 'svelte';
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher()

  export let sudoku;
  export let solvedSudoku
  export let isSolvedByClick
  let inputSudoku = JSON.parse(JSON.stringify(sudoku))
  let numLeft = {
    1: 9,
    2: 9,
    3: 9,
    4: 9,
    5: 9,
    6: 9,
    7: 9,
    8: 9,
    9: 9,
  }
  let numLeftCopy = JSON.parse(JSON.stringify(numLeft))

  function howManyLeft(value, isDel){
    
    if(value == null){
      inputSudoku.map((arr)=>{
        arr.map((num)=>{
          if(num != null){
            numLeft[num] -= 1
          }
        })
      })

      dispatch('numLeft', numLeft)
    }else if(isDel){
      numLeft[value] += 1
      dispatch('numLeft', numLeft)
    }else{
      if(numLeft[value] > 0){
        numLeft[value] -= 1
      }
      dispatch('numLeft', numLeft)
    }
    numLeftCopy = JSON.parse(JSON.stringify(numLeft))
  }

  onMount(()=>{howManyLeft(null)})
  
  let rows = Array(9).fill(Array(9).fill(''));
  let cellElements = {};
  const cellId = (rowIndex, cellIndex) => `${rowIndex}-${cellIndex}`;
  let isWon = false

  const onKeyDown = (event, rowIndex, cellIndex) => {
    numLeft = JSON.parse(JSON.stringify(numLeftCopy))
    if ('123456789'.includes(event.key) && inputSudoku[rowIndex][cellIndex] == null && numLeft[event.key]-1 >= 0) {
      inputSudoku[rowIndex][cellIndex] = Number(event.key)
      rows[rowIndex] = rows[rowIndex].map((cell, ci) => ci === cellIndex ? event.key : cell)
      
      checkAnswer(event.key,rowIndex,cellIndex)
      howManyLeft(event.key,false)
      
      checkWin()
    }
    if (event.key === 'ArrowLeft' && cellIndex > 0) {
      cellElements[cellId(rowIndex, cellIndex - 1)].focus();
    }
    if (event.key === 'ArrowUp' && rowIndex > 0) {
      cellElements[cellId(rowIndex - 1, cellIndex)].focus();
    }
    if ((event.key === 'ArrowRight' || event.key === 'Tab') && cellIndex < 8) {
      cellElements[cellId(rowIndex, cellIndex + 1)].focus();
    }
    if ((event.key === 'ArrowDown' || event.key === 'Enter') && rowIndex < 8) {
      cellElements[cellId(rowIndex + 1, cellIndex)].focus();
    }
    if((event.key === 'Backspace' || event.key === 'Delete') && !isSolvedByClick){
      if(rows[rowIndex][cellIndex] != '' && rows[rowIndex][cellIndex] != null){
        howManyLeft(rows[rowIndex][cellIndex],true)

        rows[rowIndex][cellIndex] = null;
        inputSudoku[rowIndex][cellIndex] = null

        const currentCell = document.getElementById(`${rowIndex}_${cellIndex}`)

        if(![...currentCell.classList].includes('prefilled')){
          document.getElementById(`${rowIndex}_${cellIndex}`).style.backgroundColor = '' 
        }
      }
      
    }
  }

  function checkAnswer(value, rowIndex, cellIndex){
    const currentCell = document.getElementById(`${rowIndex}_${cellIndex}`)
    
    if(![...currentCell.classList].includes('prefilled')){
      if(value == solvedSudoku[rowIndex][cellIndex]){
        currentCell.style.backgroundColor = '#a7c957'
        currentCell.style.color = 'black'
      }else{
        currentCell.style.backgroundColor = '#bc4749'
        currentCell.style.color = 'black'
      }
    }
  }

  Array.prototype.equals = function (array) {
    if (!array)
        return false;
    if(array === this)
        return true;
    if (this.length != array.length)
        return false;

    for (var i = 0, l=this.length; i < l; i++) {
        if (this[i] instanceof Array && array[i] instanceof Array) {
            if (!this[i].equals(array[i]))
                return false;       
        }           
        else if (this[i] != array[i]) { 
            return false;   
        }           
    }       
    return true;
}

  function checkWin(){
    if(solvedSudoku.equals(inputSudoku)){
        isWon = true
      }
  }
</script>

<main>
  {#if isWon}
    <div id="win--board">
      <Confetti iterationCount=infinite/>
      <h1>Congratulations! You won!</h1>
      <Confetti iterationCount=infinite/>
    </div>
    
  {/if}
    
  <div id="board" >
    {#each rows as row, rowIndex}
        <div class:border-bottom={(rowIndex + 1) % 3 === 0}>
          {#each row as cell, cellIndex}
              <input
                id='{rowIndex}_{cellIndex}'
                bind:this={cellElements[cellId(rowIndex, cellIndex)]}
                type='text'
                class='cell'
                class:border-right={(cellIndex + 1) % 3 === 0}
                class:prefilled={sudoku[rowIndex][cellIndex]}
                value={sudoku[rowIndex][cellIndex] || cell}
                on:keydown|preventDefault={e => onKeyDown(e, rowIndex, cellIndex)}
          />
          {/each}
        </div>
      {/each}
  </div>

</main>

<style>
    main {
    text-align: center;
  }
  #board{
    min-width: 450px;
  }
  #win--board{
    width: 90vw;
    height: 90vh;
    position: fixed;
    top: 5vh;
    left: 5vw;
    background-color: grey;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .cell {
    display: inline-block;
    height: 50px;
    width: 50px;
    border: 1px solid black;
    margin: 0;
    text-align: center;
  }
  .border-bottom {
    border-bottom: 2px solid black;
  }
  .border-right {
    border-right: 2px solid black;
  }
  .prefilled {
    background-color: beige;
  }
  .correct{
    background-color: #a7c957 !important; 
    color: black !important;
  }
  .wrong{
    background-color: #bc4749 !important;
    color: black !important;
  }
</style>