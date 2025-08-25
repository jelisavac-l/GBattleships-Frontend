<template>
  <div id="app">
    <template v-if="currentPage === 'home'">
      <!-- <Home @join-clicked="showGame" @host-clicked="showGame" /> -->
       <Home @join-clicked="showGame" @host-clicked="createGame"/>
    </template>
    <template v-else-if="currentPage === 'game'">
      <Game />
    </template>
    <template v-else-if="currentPage === 'created'">
      <Status :id=currentGameID />
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
const currentGameID = ref(null)
const gameData = ref(null);
const error = ref(null);

const generateRandomUsername = () => {
  const adjectives = ['Cool', 'Brave', 'Smart', 'Quick', 'Sassy', 'Gentle', 'Funny'];
  const nouns = ['Panda', 'Tiger', 'Lion', 'Wolf', 'Fox', 'Dragon', 'Phoenix'];

  const randomAdjective = adjectives[Math.floor(Math.random() * adjectives.length)];
  const randomNoun = nouns[Math.floor(Math.random() * nouns.length)];

  return `${randomAdjective}${randomNoun}${Math.floor(Math.random() * 100)}`;
};

const createGame = async () => {
  // Clear any previous errors or data by assigning a new value to the .value property
  error.value = null;
  gameData.value = null;

  // 1. Generate the random parameters
  const gameId = Math.floor(Math.random() * 100000).toString(); // A random integer for the ID
  const username = generateRandomUsername(); // A random username

  const requestBody = {
    id: gameId,
    username: username
  };

  console.log('Sending request with:', requestBody);

  // 2. Send the POST request using fetch
  try {
    const response = await fetch('http://localhost:8080/game', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(requestBody),
    });

    // 3. Handle the response
    if (!response.ok) {
      // If the response is not OK (e.g., 404, 500), throw an error
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data = await response.json();
    gameData.value = data;

    if (data && data.gid ) {
      currentGameID.value = data.gid;
      console.log('Successfully created game with GID:', data.gid);
    } else {
      console.error('Response did not contain a GID.');
    }

    currentPage.value = 'created'
    
  } catch (err) {
    // 4. Catch and handle any network or HTTP errors
    error.value = 'Failed to create game. Please check the server connection.';
    console.error('There was a problem with the fetch operation:', err);
  }


};

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