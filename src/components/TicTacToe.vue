<template>
  <div>
    <form style="font-size: 25px; margin: 20px;">
      <label for="row">Nhập số hàng:</label>
      <input style="height: 20px; font-size: 20px;" type="text" id="row" v-model.number="rowCount" @input="updateBoard" />
      <label for="col">Nhập số cột:</label>
      <input style="height: 20px; font-size: 20px;" type="text" id="col" v-model.number="colCount" @input="updateBoard" />
      <button @click="resetGame">Oke</button>
    </form>

    <div class="all">
      <div class="div-left">
        <div class="board" v-if="winner">
          <div class="row" v-for="(row, rowIndex) in board" :key="rowIndex">
            <div class="cell" v-for="(cell, colIndex) in row" 
                :key="colIndex" 
                :class="{'highlighted':isHighlighted(rowIndex, colIndex)}"
                >
              {{ cell }}
            </div>
          </div>
        </div>
        <div class="board" v-else>
          <div class="row" v-for="(row, rowIndex) in board" :key="rowIndex">
            <div class="cell" v-for="(cell, colIndex) in row" 
                :key="colIndex" 
                @click="handleClick(rowIndex, colIndex)">
              {{ cell }}
            </div>
          </div>
        </div>
      </div>

      <div class="div-right">
        <div class="message" v-if="winner">
          <p style="font-size: 25px; color: red;">Winner: {{ winner }}</p>
          <button style="height: 60px; width: 150px; font-size: 25px; margin: 30px;" @click="resetGame">Restart</button>
        </div>
        <div class="message" v-else>
          <p style="font-size: 30px; color: red;">Position Curent: {{ step.ro + 1 }} - {{ step.co + 1 }}</p>
          <button style="height: 60px; width: 150px; font-size: 25px; margin: 30px;" @click="resetGame">Restart</button>
        </div>

        <h1 class="txtHisStep">His steps</h1>
        <div class="list-container">
          <ul class="list">
            <li v-for="(hisTep, id) in hisSteps" :key="id" class="list-item">
              <strong style="color: red" v-if="hisTep.co === step.co && hisTep.ro === step.ro">{{hisTep.ro + 1}} - {{hisTep.co + 1}}</strong>
              <p v-else>{{hisTep}}</p>
            </li>
          </ul>
        </div>
        <button class="sort" @click="changeSort">Sort</button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, reactive, watch } from 'vue';

export default {
   name:'TicTacToe',
  setup() {
    const rowCount = ref(10);
    const colCount = ref(10);
    const board = reactive([]);
    const player = ref('X');
    const winner = ref(null);
    const step = reactive({});
    const hisSteps = ref([])
    const saveWinSteps = ref([])
    const enumm = ref(0)
    const defaultHisStep = ref([])

    const updateBoard = () => {
      board.splice(0);
      for (let i = 0; i < rowCount.value; i++) {
        const row = [];
        for (let j = 0; j < colCount.value; j++) {
          row.push(null);
        }
        board.push(row);
      }
    };

    const handleClick = (row, col) => {
      if (!board[row][col] && !winner.value) {
        step.ro = row;
        step.co = col;

        const newStep = { ...step };

        defaultHisStep.value.push(newStep);

        hisSteps.value.push(newStep);

        board[row][col] = player.value;
        checkWinner(row, col);
        player.value = player.value === 'X' ? 'O' : 'X';
      }
    };

    const checkWinner = (row, col) => {
      const symbol = board[row][col];

      let won = false;

      // Kiểm tra hàng ngang
      won = won || checkLine(row, col, 0, 1) || checkLine(row, col, 0, -1);

      // Kiểm tra hàng dọc
      won = won || checkLine(row, col, 1, 0) || checkLine(row, col, -1, 0);

      // Kiểm tra đường chéo chính
     won = won || checkLine(row, col, 1, 1) || checkLine(row, col, -1, -1);

      // Kiểm tra đường chéo phụ
      won = won || checkLine(row, col, 1, -1) || checkLine(row, col, -1, 1);

      if (won) {
        winner.value = symbol;
      }
    };

    const checkLine = (row, col, dr, dc) => {
      const symbol = board[row][col];

        saveWinSteps.value.push({
            ro: row,
            co: col
        })

      let count = 1;
      let limit1 = 1
      let limit2 = 1

      let i = row + dr;
      let j = col + dc;
      while (
        i >= 0 &&
        i < board.length &&
        j >= 0 &&
        j < board[0].length &&
        board[i][j] === symbol
        && limit1 <= 5
      ) {

        ++limit1

        saveWinSteps.value.push({
            ro: i,
            co: j
        })

        count++;
        i += dr;
        j += dc;
      }

      i = row - dr;
      j = col - dc;
      while (
        i >= 0 &&
        i < board.length &&
        j >= 0 &&
        j < board[0].length &&
        board[i][j] === symbol &&
        limit2 <= 5
      ) {
        ++limit2

        saveWinSteps.value.push({
            ro: i,
            co: j
        })

        count++;
        i -= dr;
        j -= dc;
      }
        if(count < 5){
            saveWinSteps.value = []
        }
        
      return count === 5;
    };

    const isHighlighted = (row, col) => {
      return saveWinSteps.value.some((step) => step.ro === row && step.co === col);
    }

    const resetGame = () => {
      board.splice(0);
      player.value = 'X';
      winner.value = null;
      step.ro = undefined;
      step.co = undefined;
      hisSteps.value = []
      saveWinSteps.value = []
      defaultHisStep.value = []
      enumm.value = 0
      updateBoard();
    };

    watch(rowCount, updateBoard);
    watch(colCount, updateBoard);

    updateBoard();

    const changeSort = () => {
      ++enumm.value
      if(enumm.value === 3){
        enumm.value = 0
        hisSteps.value = [...defaultHisStep.value]
      }else{
        if(enumm.value === 1){
          hisSteps.value.sort(function(a, b){
            let x = a.ro;
            let y = b.ro;
            if (x < y) {return -1;}
            if (x > y) {return 1;}
            return 0;
          })
        }else if(enumm.value === 2){
          hisSteps.value.sort(function(a, b){
            let x = a.co;
            let y = b.co;
            if (x < y) {return -1;}
            if (x > y) {return 1;}
            return 0;
          })
        }
      }
    }

    return {
      rowCount,
      colCount,
      board,
      player,
      winner,
      step,
      handleClick,
      resetGame,
      hisSteps,
      saveWinSteps,
      isHighlighted,
      enumm,
      changeSort,
      defaultHisStep
    };
  },
};
</script>

<style scoped>
.board {
  display: flex;
  flex-direction: column;
}

.row {
  display: flex;
}

.cell {
  width: 50px;
  height: 50px;
  border: 1px solid black;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 20px;
  cursor: pointer;
}

.list-container {
  width: 300px; 
  height: 300px; 
  text-align: center;
  overflow: auto;
  font-size: 20px;
}

.list {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.list-item {
  padding: 10px;
  border-bottom: 1px solid #ccc;
}

.highlighted {
  background-color: yellow;
  font-weight: bold;
}

.sort{
  margin-left: 0px
}

.all{
  display: flex;
  justify-content: center;
}

.div-right{
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin-left: 200px;
}

.txtHisStep{
  margin-left: 80px;
  font-size: 40px;
}

</style>
