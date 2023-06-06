<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { BoardSquare } from '../models/BoardSquare';
import { GameState } from '../models/GameState';

const props = defineProps<{
  playerX: string
  playerO: string
}>();

const emit = defineEmits(['gameOver']);
const showScoreBoard = ref(false);
const currentPlayer = ref('');
const activeGameBoard = localStorage.getItem('activeGame');
const currentPlayerStore = localStorage.getItem('currentPlayer');
let gameState: GameState;
let currentGameState = localStorage.getItem('gameState');
let playerXWins = localStorage.getItem('PlayerXScore') ? Number(localStorage.getItem('PlayerXScore')) : 0;
let playerOWins = localStorage.getItem('PlayerOScore') ? Number(localStorage.getItem('PlayerOScore')) : 0;
let winnerScore = 0;
let isXPlaying: boolean;

if (currentGameState === GameState.gameOver) {
  gameState = GameState.gameOver;
  emit('gameOver');
} else if (currentGameState === GameState.draw) {
  gameState = GameState.draw;
} else {
  gameState = GameState.active;
}

localStorage.setItem('gameState', gameState);

const squares = ref<BoardSquare[]>(activeGameBoard ? JSON.parse(activeGameBoard) : [
  new BoardSquare(1), new BoardSquare(2), new BoardSquare(3),
  new BoardSquare(4), new BoardSquare(5), new BoardSquare(6),
  new BoardSquare(7), new BoardSquare(8), new BoardSquare(9),
]);

onMounted(() => {
  if (currentPlayerStore === props.playerO) {
    currentPlayer.value = props.playerO;
    isXPlaying = false;
  } else {
    currentPlayer.value = props.playerX;
    isXPlaying = true;
  }

  if (!gameState) {
    gameState = GameState.active;
  }
})

const showScore = () => {
  showScoreBoard.value = !showScoreBoard.value;
}

const resetGame = () => {
  for (const square of squares.value) {
    square.checked = '';
  }
  gameState = GameState.active;
  localStorage.setItem('gameState', gameState);
  localStorage.setItem('activeGame', JSON.stringify(squares.value));
}

const resetAll = () => {
  for (const square of squares.value) {
    square.checked = '';
  }

  playerXWins = 0;
  playerOWins = 0;
  gameState = GameState.active;
  localStorage.setItem('gameState', gameState);
}

const makeMove = (square: BoardSquare) => {
  if (square.checked !== '') {
    return;
  }

  square.checked = isXPlaying ? 'X' : 'O';

  checkWin(square.checked);

  if (gameState === GameState.gameOver) {
    localStorage.setItem('currentPlayer', currentPlayer.value);
    localStorage.setItem('activeGame', JSON.stringify(squares.value));
    return;
  }

  if (checkDraw()) {
    localStorage.setItem('currentPlayer', currentPlayer.value);
    localStorage.setItem('activeGame', JSON.stringify(squares.value));
    return;
  }

  currentPlayer.value = isXPlaying ? props.playerO : props.playerX;
  isXPlaying = !isXPlaying;

  localStorage.setItem('currentPlayer', currentPlayer.value);
  localStorage.setItem('activeGame', JSON.stringify(squares.value));
}

const checkWin = (sign: string) => {
  if ((squares.value[0].checked === sign && squares.value[1].checked === sign && squares.value[2].checked === sign)
    || (squares.value[3].checked === sign && squares.value[4].checked === sign && squares.value[5].checked === sign)
    || (squares.value[6].checked === sign && squares.value[7].checked === sign && squares.value[8].checked === sign)
    || (squares.value[0].checked === sign && squares.value[3].checked === sign && squares.value[6].checked === sign)
    || (squares.value[1].checked === sign && squares.value[4].checked === sign && squares.value[7].checked === sign)
    || (squares.value[2].checked === sign && squares.value[5].checked === sign && squares.value[8].checked === sign)
    || (squares.value[0].checked === sign && squares.value[4].checked === sign && squares.value[8].checked === sign)
    || (squares.value[2].checked === sign && squares.value[4].checked === sign && squares.value[6].checked === sign)
  ) {
    gameState = GameState.gameOver;
    localStorage.setItem('gameState', gameState);
    emit('gameOver');

    if (sign === 'X') {
      playerXWins++;
      winnerScore = playerXWins;
    }
    if (sign === 'O') {
      playerOWins++;
      winnerScore = playerOWins;
    }
    localStorage.setItem('Player' + sign + 'Score', JSON.stringify(winnerScore))
  }
}

const checkDraw = (): boolean => {
  if (squares.value.find((s) => s.checked === '')) {
    return false;
  }
  gameState = GameState.draw;
  localStorage.setItem('gameState', gameState);
  emit('gameOver');
  return true;
}

defineExpose({
  showScore,
  resetGame,
  resetAll
});
</script>

<template>
  <h2 v-if="gameState === GameState.active">It is {{ currentPlayer }}s turn</h2>
  <h2 v-else-if="gameState === GameState.gameOver">{{ currentPlayer }} won!</h2>
  <h2 v-else-if="gameState === GameState.draw">It's a draw!</h2>
  <div class="board-container">
    <button
      v-for="square in squares"
      :key="square.position"
      class="square"
      :disabled="gameState === GameState.gameOver || gameState === GameState.draw"
      @click="() => {
        makeMove(square)
      }"
    >{{ square.checked }}</button>
  </div>

  <div class="score-board" v-if="showScoreBoard">
    <h3>SCORE BOARD</h3>
    <p>{{ props.playerX }}: {{ playerXWins }}/{{ playerXWins + playerOWins }}</p>
    <p>{{ props.playerO }}: {{ playerOWins }}/{{ playerXWins + playerOWins }}</p>
  </div>
</template>

<style scoped>
.board-container {
  display: grid;
  gap: 1px;
  grid-template-columns: repeat(3, 1fr);
}

.square {
  width: 100px;
  height: 100px;
  background-color: grey;
}

.square:disabled {
  color: rgba(255, 255, 255, 0.87);
}

.score-board {
  margin-top: 50px;
  border: 1px solid grey;
}
</style>
