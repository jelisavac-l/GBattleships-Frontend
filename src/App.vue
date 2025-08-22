<template>
  <div id="app">
    <template v-if="currentPage === 'home'">
      <!-- <Home @join-clicked="showGame" @host-clicked="showGame" /> -->
       <BoardCreator />
    </template>
    <template v-else-if="currentPage === 'game'">
      <Game />
    </template>
    <template v-else-if="currentPage === 'setup'">
      <BoardCreator />
    </template>
  </div>
</template>

<script setup>
import Game from './components/Game.vue'
import BoardCreator from './components/BoardCreator.vue';
import Home from './components/home/Home.vue';
import {ref} from 'vue';


const currentPage = ref('home');
const showGame = () => {
  currentPage.value = 'setup';
};
</script>

<style scoped>
#app {
  text-align: center;
  font-family: 'Courier New', Courier, monospace;
}
</style>