<script>
    export let sudoku;
    export let solvedSudoku
    
    let rows = Array(9).fill(Array(9).fill(''));
    let cellElements = {};
    const cellId = (rowIndex, cellIndex) => `${rowIndex}-${cellIndex}`;

    const onKeyDown = (event, rowIndex, cellIndex) => {
      if ('123456789'.includes(event.key)) {
        rows[rowIndex] = rows[rowIndex].map((cell, ci) => ci === cellIndex ? event.key : cell)

        checkAnswer(event.key,rowIndex,cellIndex)
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
      if(event.key === 'Backspace' || event.key === 'Delete'){
        rows[rowIndex][cellIndex] = null;
        const currentCell = document.getElementById(`${rowIndex}_${cellIndex}`)

        if(![...currentCell.classList].includes('prefilled')){
          document.getElementById(`${rowIndex}_${cellIndex}`).style.backgroundColor = 'white' 
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
</script>

<main>
    
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
    text-shadow: 0 0 0 black;
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