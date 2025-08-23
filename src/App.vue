<template>
  <div id="app">
    <template v-if="currentPage === 'home'">
      <!-- <Home @join-clicked="showGame" @host-clicked="showGame" /> -->
       <Home @join-clicked="showGame" @host-clicked="showGameCreated"/>
    </template>
    <template v-else-if="currentPage === 'game'">
      <Game />
    </template>
    <template v-else-if="currentPage === 'created'">
      <Status id="1"/>
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
import Status from './components/Status.vue';


const currentPage = ref('home');

const showGame = () => {
  currentPage.value = 'game';
};

const showSetup = () => {
  currentPage.value = 'setup'
}

const showGameCreated = () => {
  currentPage.value = 'created'
}

</script>

<style scoped>
@font-face {
    font-family: Minecraft;
    src: url('./assets/fonts/MinecraftRegular-Bmg3.otf');
    font-weight: 500;
}

@font-face {
    font-family: Minecraft;
    src: url('./assets/fonts/MinecraftBold-nMK1.otf');
    font-weight: bold;
}

#app {
  text-align: center;
  font-family: Minecraft;
  font-size: larger;
}
</style>