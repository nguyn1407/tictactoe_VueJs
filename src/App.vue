<template>
  <div>
    <TicTacToe/>
  </div>
</template>

<script>
import TicTacToe from './components/TicTacToe.vue'

export default {
  name: 'App',
  components: {
    TicTacToe
  }
}
</script>

<style>
.board {
  margin-top: 100px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.row {
  display: flex;
}

.cell {
  width: 60px;
  height: 60px;
  border: 1px solid #e41212;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  cursor: pointer;
}

.message {
  margin-top: 20px;
  text-align: center;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}

</style>
