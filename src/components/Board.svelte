<script>
  import { Confetti } from "svelte-confetti"
  import { onMount } from 'svelte';
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher()

  export let sudoku;
  export let solvedSudoku
  let inputSudoku = JSON.parse(JSON.stringify(sudoku))
  let hintsSudoku = JSON.parse(JSON.stringify(sudoku))
  export let isSolvedByClick
  export let isHints
  $: isHints = isHints
  
  export let hints
  $: {
    if(hints != null){
      hintsSudoku = JSON.parse(JSON.stringify(hints))
      rows = JSON.parse(JSON.stringify(hintsSudoku))
    }
  }

  let numLeft = {1: 9, 2: 9, 3: 9, 4: 9, 5: 9, 6: 9, 7: 9, 8: 9, 9: 9,}
  let numLeftCopy = JSON.parse(JSON.stringify(numLeft))
  let rows = Array(9).fill(Array(9).fill(''));
  let cellElements = {};
  const cellId = (rowIndex, cellIndex) => `${rowIndex}-${cellIndex}`;
  let isWon = false

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
  
  const onKeyDown = (event, rowIndex, cellIndex) => {
    const currentCell = document.getElementById(`${rowIndex}_${cellIndex}`)

    if(('123456789'.includes(event.key) || event.key === 'Backspace' || event.key === 'Delete') && isHints && ![...currentCell.classList].includes('prefilled') && !isSolvedByClick && (currentCell.style.backgroundColor != 'rgb(188, 71, 73)' && currentCell.style.backgroundColor != 'rgb(167, 201, 87)')){

      if('123456789'.includes(event.key)){
        if(rows[rowIndex][cellIndex] == '' || rows[rowIndex][cellIndex] == null){
          hintsSudoku[rowIndex][cellIndex] = event.key; 
          rows[rowIndex] =  rows[rowIndex].map((cell, ci) => ci === cellIndex ? event.key : cell)
        }else{

          hintsSudoku[rowIndex][cellIndex] += `,${event.key}`; 
          
          rows[rowIndex] = rows[rowIndex].map((cell, ci) => {
            if(ci === cellIndex){
              return hintsSudoku[rowIndex][cellIndex]
            }else{
              return cell
            }
          })
        }
        currentCell.style.backgroundColor = 'cornflowerblue'
        currentCell.style.textAlign = 'left'
      }
      if((event.key === 'Backspace' || event.key === 'Delete') && !isSolvedByClick && hintsSudoku[rowIndex][cellIndex] != null && rows[rowIndex][cellIndex] != ''){

        hintsSudoku[rowIndex][cellIndex] = hintsSudoku[rowIndex][cellIndex].substring(0,event.target.selectionStart-2)
         
        rows[rowIndex] = rows[rowIndex].map((cell, ci) => {
          if(ci === cellIndex){
            return hintsSudoku[rowIndex][cellIndex]
          }else{
            return cell
          }
        })

        if(hintsSudoku[rowIndex][cellIndex] == null || hintsSudoku[rowIndex][cellIndex] == ''){
          currentCell.style.backgroundColor = ''
        }
      }
      dispatch('hints', hintsSudoku)

    }else if(!isSolvedByClick){
      currentCell.style.textAlign = 'center'
      numLeft = JSON.parse(JSON.stringify(numLeftCopy))

      if ('123456789'.includes(event.key) && inputSudoku[rowIndex][cellIndex] == null && numLeft[event.key]-1 >= 0) {
        inputSudoku[rowIndex][cellIndex] = Number(event.key)
        rows[rowIndex] = rows[rowIndex].map((cell, ci) => ci === cellIndex ? event.key : cell)
        
        checkAnswer(event.key,rowIndex,cellIndex)
        howManyLeft(event.key,false)
        
        checkWin()
      }
      if((event.key === 'Backspace' || event.key === 'Delete') && !isSolvedByClick){
        if(rows[rowIndex][cellIndex] != '' && rows[rowIndex][cellIndex] != null){
          howManyLeft(rows[rowIndex][cellIndex],true)
          rows[rowIndex][cellIndex] = '';
          inputSudoku[rowIndex][cellIndex] = null

          if(![...currentCell.classList].includes('prefilled')){
            document.getElementById(`${rowIndex}_${cellIndex}`).style.backgroundColor = '' 
          }
        }
      }
      dispatch('hints', rows)
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
    width: 100%;
    height: 100%;
    position: fixed;
    top: 70px;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 10;
    font-size: 30px;
    color: white;
  }
  .cell {
    display: inline-block;
    height: 50px;
    width: 50px;
    border: 1px solid black;
    margin: 0;
    text-align: center;
    background-color: #f3e9dc;
  }
  .border-bottom {
    border-bottom: 2px solid #774936;
  }
  .border-right {
    border-right: 2px solid #774936;
  }
  .prefilled {
    background-color: #cd9777;
  }
</style>