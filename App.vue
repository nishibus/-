<template>
  <div id="app">
    <n-space vertical>
      <n-card title="井字棋" bordered>
        <div class="board">
          <div v-for="(row, rowIndex) in board" :key="rowIndex" class="row">
            <n-button
              v-for="(cell, colIndex) in row"
              :key="colIndex"
              :disabled="cell !== ''"
              @click="makeMove(rowIndex, colIndex)"
              class="cell"
              :style="getCellStyle(cell)"
            >
              {{ cell }}
            </n-button>
          </div>
        </div>
      </n-card>
      <n-button @click="resetGame">重新开始</n-button>
      <div v-if="winner" class="winner">获胜者: {{ winner }}</div>
      <div v-else-if="isDraw" class="draw">平局</div>
    </n-space>
  </div>
</template>

<script>
import { ref } from 'vue';
import { NButton, NCard, NSpace } from 'naive-ui';

export default {
  name: 'App',
  components: {
    NButton,
    NCard,
    NSpace,
  },
  setup() {
    const board = ref([
      ['', '', ''],
      ['', '', ''],
      ['', '', ''],
    ]);
    const currentPlayer = ref('X');
    const winner = ref(null);
    const isDraw = ref(false);

    const makeMove = (row, col) => {
      if (board.value[row][col] || winner.value) return;
      board.value[row][col] = currentPlayer.value;
      if (checkWinner()) {
        winner.value = currentPlayer.value;
      } else if (board.value.flat().every(cell => cell !== '')) {
        isDraw.value = true;
      } else {
        currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X';
      }
    };

    const checkWinner = () => {
      // 检查行、列和对角线
      for (let i = 0; i < 3; i++) {
        if (board.value[i][0] === board.value[i][1] && board.value[i][1] === board.value[i][2] && board.value[i][0] !== '') return true;
        if (board.value[0][i] === board.value[1][i] && board.value[1][i] === board.value[2][i] && board.value[0][i] !== '') return true;
      }
      if (board.value[0][0] === board.value[1][1] && board.value[1][1] === board.value[2][2] && board.value[0][0] !== '') return true;
      if (board.value[0][2] === board.value[1][1] && board.value[1][1] === board.value[2][0] && board.value[0][2] !== '') return true;
      return false;
    };

    const resetGame = () => {
      board.value = [['', '', ''], ['', '', ''], ['', '', '']];
      currentPlayer.value = 'X';
      winner.value = null;
      isDraw.value = false;
    };

    const getCellStyle = (cell) => {
      return {
        'background-color': cell === 'X' ? 'lightblue' : (cell === 'O' ? 'lightgreen' : 'white'),
        'width': '60px',
        'height': '60px',
        'font-size': '24px',
        'border-radius': '8px',
        'margin': '4px',
      };
    };

    return { board, currentPlayer, winner, isDraw, makeMove, resetGame, getCellStyle };
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
  width: 60px;
  height: 60px;
  margin: 5px;
  font-size: 24px;
}
.winner, .draw {
  margin-top: 20px;
  font-size: 18px;
}
</style>
