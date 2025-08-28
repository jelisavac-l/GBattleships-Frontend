<template>
  <div id="app">
    <template v-if="currentPage === 'home'">
      <!-- <Home @join-clicked="showGame" @host-clicked="showGame" /> -->
      <Home @join-clicked="joinGame" @host-clicked="createGame" />
    </template>
    <template v-else-if="currentPage === 'game'">
      <Game :board=currentBoard @move="sendMove" :your-turn=yourTurn :last-turn=lastTurn :enemy-turn=lastEnemyTurn />
    </template>
    <template v-else-if="currentPage === 'created'">
      <Status :id=currentGameID />
    </template>
    <template v-else-if="currentPage === 'setup'">
      <BoardCreator @submit-board="submitBoard"/>
    </template>
    <template v-else-if="currentPage === 'victory'">
      <Status msg="You won!" />
    </template>
    <template v-else-if="currentPage === 'defeat'">
      <Status msg="You lost!" />
    </template>
    <template v-else-if="currentPage === 'disconnected'">
      <Status msg="Error: other player closed the connection to the server!" />
    </template>
  </div>
</template>

<script setup>
import Game from './components/Game.vue'
import BoardCreator from './components/BoardCreator.vue';
import Home from './components/home/Home.vue';
import { ref } from 'vue';
import Status from './components/Status.vue';

const server = 'localhost:8080';

const currentPage = ref('home');
const ws = ref(null);
const currentGameID = ref(null);
const gameData = ref(null);
const error = ref(null);
const currentBoard = ref(null)
const yourTurn = ref(false)
const lastTurn = ref([])
const tempLastTurn = ref([])
const generatedUsername = ref(null)
const lastEnemyTurn = ref([-1, -1, -1])

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
  generatedUsername.value = username
  const requestBody = {
    id: gameId,
    username: username
  };

  console.log('Sending request with:', requestBody);

  // 2. Send the POST request using fetch
  try {
    const response = await fetch(`http://${server}/game`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(requestBody),
    });

    // 3. Handle the response
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data = await response.json();
    gameData.value = data;

    if (data && data.gid) {
      currentGameID.value = data.gid;
      console.log('Successfully created game with GID:', data.gid);

      // Websocket
      console.log('Attempting to establish websocket connection...')
      const wsUrl = `ws://${server}/${currentGameID.value}/player1?id=${requestBody.username}&username=${username}`;
      ws.value = new WebSocket(wsUrl);

      // Listeners
      ws.value.onopen = (event) => {
        console.log('WebSocket connection opened!', event);
      };

      ws.value.onmessage = (event) => {
        handleWebSocketRequest(event)
      };

      ws.value.onclose = (event) => {
        console.log('WebSocket connection closed.', event);
      };

      ws.value.onerror = (error) => {
        console.error('WebSocket error:', error);
      };
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

const joinGame = (gid) => {

  const gameId = Math.floor(Math.random() * 100000).toString(); // A random integer for the ID
  const username = generateRandomUsername(); // A random username
  generatedUsername.value = username
  const requestBody = {
    id: gameId,
    username: username
  };
  const wsUrl = `ws://${server}/${gid}/player2?id=${requestBody.username}&username=${username}`;
  ws.value = new WebSocket(wsUrl);

  // Listeners
  ws.value.onopen = (event) => {
    console.log('WebSocket connection opened!', event);
  };

  ws.value.onmessage = (event) => {
    handleWebSocketRequest(event)
    // TODO: Add message logic
  };

  ws.value.onclose = (event) => {
    console.log('WebSocket connection closed.', event);
  };

  ws.value.onerror = (error) => {
    console.error('WebSocket error:', error);
  };
}

// Handling websocket messages
const handleWebSocketRequest = (event) => {

  const messageType = JSON.parse(event.data).type.toString()
    console.log(messageType)
    switch (messageType) {
      case 'GetBoardMessage':
        console.log('Requesting board...');
        showSetup()
        break;
      case 'gameStartedMessage':
        showGame()
        break;
      case 'GetTurnMessage':
        console.log(event)
        yourTurn.value = true
        const x = JSON.parse(event.data).payload.y
        const y = JSON.parse(event.data).payload.x
        const hit = JSON.parse(event.data).payload.hit
        const hitNumemric = hit ? 2 : 3
        lastEnemyTurn.value = [x, y, hitNumemric]
      break;
      case 'TurnResultMessage':
        const result = JSON.parse(event.data).payload.hit
        const resultNumeric = result ? 2 : 3
        lastTurn.value = [tempLastTurn[0], tempLastTurn[1], resultNumeric]
        console.log(lastTurn.value)
      break;
      case 'GameResultMessage':
        const winner = JSON.parse(event.data).payload.winneruname
        showGameResults(winner === generatedUsername.value)
      break;
      default:
        break;
    }
};

const showGameResults = (win) => {
  currentPage.value = win ? 'victory' : 'defeat'
}

const submitBoard = (board) => {

  currentBoard.value = board;

  if (!ws.value || ws.value.readyState !== WebSocket.OPEN) {
    console.error("Cannot send message: WebSocket is not connected or ready.");
    return;
  }
  // Create numeric board
  const numericBoard = board.map((row) => row.map((cell) => {
      switch (cell.status) {
        case "ship": return 1;
        case "hit": return 2;
        case "miss": return 3;
        case "empty": return 0; 
        default: return 0;
      }
  })

  );

  const transferObject = {
    Type: "SendBoardMessage",
    Payload: {
      Cells: numericBoard
    }
  }

  const jsonString = JSON.stringify(transferObject);
  console.log(jsonString);

  // Send json here
  ws.value.send(jsonString)

}

function sendMove({ x, y }) {
  tempLastTurn[0] = x
  tempLastTurn[1] = y
  yourTurn.value = false;
  
  // Check if the WebSocket connection is open
  if (!ws || ws.value.readyState !== WebSocket.OPEN) {
    console.error("WebSocket is not connected. Cannot send turn message.");
    return;
  }

  const turnMessage = {
    Type: "SendTurnMessage",
    Payload: {
      x: y, // Note: DRZI VODU
      y: x,
    },
  };

  const jsonString = JSON.stringify(turnMessage);
  console.log("Sending turn:", jsonString);
  ws.value.send(jsonString);
}

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