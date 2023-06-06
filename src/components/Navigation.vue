<script setup lang="ts">
const props = defineProps<{
  isGameOver: boolean
}>();

defineEmits(['showScore', 'startNewGame', 'startOver']);

</script>

<template>
  <nav class="navigation-container">
    <ul class="navigation">
      <li
        class="nav-links"
        @click="$emit('showScore')"
      >Show score</li>
      <li
        class="nav-links"
        v-if="props.isGameOver"
        @click="$emit('startNewGame')"
      >Start new game</li>
      <li
        class="nav-links"
        @click="$emit('startOver')"
      >Start over</li>
    </ul>
  </nav>
</template>

<style scoped>
.navigation-container {
  margin-top: 50px;
}

.navigation {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

.nav-links {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6em 1.2em;
  margin: 5px 0;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  background-color: #1a1a1a;
  cursor: pointer;
  transition: border-color 0.25s;
}

.nav-links:hover {
  border-color: #646cff;
}

.nav-links:focus,
.nav-links:focus-visible {
  outline: 4px auto -webkit-focus-ring-color;
}
</style>
