<script setup lang="ts">
import { ref } from 'vue';
import GameBoard from './GameBoard.vue'
import Navigation from './Navigation.vue';

const props = defineProps<{
  playerX: string
  playerO: string
}>();

const emit = defineEmits(['endGame']);

const isGameOver = ref(false);
const isStartingNewGame = ref(false);
const gameBoard = ref<InstanceType<typeof GameBoard>>();

const handleGameOver = () => {
  isGameOver.value = true;
}

const handleShowScore = () => {
  gameBoard.value?.showScore();
}

const handleStartNewGame = () => {
  if (gameBoard.value) {
    gameBoard.value?.resetGame();
    isStartingNewGame.value = true;
  }
}

const handleStartOver = () => {
  localStorage.clear();
  emit('endGame');
  gameBoard.value?.resetAll();
}
</script>

<template>
  <main>
    <GameBoard
      ref="gameBoard"
      @game-over="handleGameOver"
      :playerX="props.playerX"
      :playerO="props.playerO"
    />
    <Navigation
      @show-score="handleShowScore"
      @start-new-game="handleStartNewGame"
      @start-over="handleStartOver"
      :isGameOver="isGameOver"
    />
  </main>
</template>

<style scoped></style>
