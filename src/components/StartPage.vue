<script setup lang="ts">
import { ref } from 'vue';
import { IPlayer } from '../models/IPlayer';

const currentSign = ref('X');
const currentName = ref('');
const playerXName = ref('');
const playerOName = ref('');
let btnText = 'Save Player';
let readyToStartGame = false;

const emit = defineEmits(['startGame']);

const savePlayer = (player: IPlayer) => {
  if (player.playerName == '') {
    alert('Please enter a name before submitting');
    return;
  }

  localStorage.setItem('Player ' + player.playerSign, player.playerName);

  if (player.playerSign === 'X') {
    playerXName.value = player.playerName;
  }
  if (player.playerSign === 'O') {
    playerOName.value = player.playerName;
  }

  currentName.value = '';

  if (readyToStartGame) {
    emit('startGame', [playerXName.value, playerOName.value]);
    return;
  }

  currentSign.value = 'O';
  btnText = 'Start Game'
  readyToStartGame = true;
}

const reset = () => {
  currentSign.value = 'X';
  currentName.value = '';

  playerXName.value = '';
  playerOName.value = '';

  btnText = 'Save Player';
  readyToStartGame = false;
}

defineExpose({
  reset
});
</script>

<template>
  <main>
    <span>Player {{ currentSign }} will be played by: </span>
    <input
      v-model="currentName"
      @keyup.enter="() => { savePlayer({ playerSign: currentSign, playerName: currentName }) }"
    />
    <button
      @click="() => { savePlayer({ playerSign: currentSign, playerName: currentName }) }"
    >{{ btnText }}</button>
  </main>
</template>

<style scoped></style>