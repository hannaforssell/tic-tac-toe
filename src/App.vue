<script setup lang="ts">
import { ref } from 'vue';
import StartPage from './components/StartPage.vue';
import GamePage from './components/GamePage.vue';

const showGamePage = ref(false);
const playerX = ref('');
const playerO = ref('');
const startPage = ref<InstanceType<typeof StartPage>>();
const gamePage = ref<InstanceType<typeof GamePage>>();
const playerXStore = localStorage.getItem('Player X');
const playerOStore = localStorage.getItem('Player O');

if (playerXStore && playerOStore) {  
  showGamePage.value = true;
  playerX.value = playerXStore;
  playerO.value = playerOStore;
}

const handleGameStart = (players: string[]) => {
  playerX.value = players[0];
  playerO.value = players[1];

  showGamePage.value = true;
}

const handleEndGame = () => {
  showGamePage.value = false;
  startPage.value?.reset();
}
</script>

<template>
  <h1>Tic Tac Toe</h1>
  <StartPage
    ref="startPage"
    v-if="!showGamePage"
    @start-game="handleGameStart" />
  <GamePage
    ref="gamePage"
    v-else
    @end-game="handleEndGame"
    :playerX="playerX"
    :playerO="playerO" />
</template>

<style scoped></style>
