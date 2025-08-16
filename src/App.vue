<template>
  <div id="app">
    <template v-if="currentPage === 'home'">
      <Home @join-clicked="showGame" @host-clicked="showGame" />
    </template>
    <template v-else-if="currentPage === 'game'">
      <Game />
    </template>
  </div>
</template>c

<script setup>
import Game from './components/Game.vue'
import Home from './components/home/Home.vue';
import {ref} from 'vue';

const currentPage = ref('home');
const showGame = () => {
  currentPage.value = 'game';
};
</script>

<style scoped>
#app {
  text-align: center;
  font-family: 'Courier New', Courier, monospace;
}
</style>