<template>
    <div class="game">
      <div>
      <h2>Your Board</h2>
      <Grid :board="playerBoard" @cell-clicked="handleCellClick" />
      </div>
      <div>
      <h2>Enemy Board</h2>
      <Grid :board="enemyBoard" :isEnemy="true" @cell-clicked="handleEnemyCellClick" />
      </div>
    </div>
    <div class="info">
      <p v-if=yourTurn><span style="color: green;">Your</span> turn.</p>
      <p v-else><span style="color: red;">Opponent's</span> turn.</p>
    </div>
  </template>
  
  <script setup>
  import { ref, watch } from 'vue'
  import Grid from './Grid.vue'

  const emit = defineEmits(['move'])

  const props = defineProps({
    board: {
      type: Array,
      required: true,
    },
    yourTurn: {
      type: Boolean,
      required: true,
    },
    lastTurn: Array,  // 
    enemyTurn: Array
  })

  const gridSize = 10
  
  // Initialize empty 10x10 boards
  const createEmptyBoard = () =>
    Array.from({ length: gridSize }, () =>
      Array.from({ length: gridSize }, () => ({
        status: 'empty', // empty, ship, hit, miss
      }))
    )
  
  const playerBoard = ref(props.board)
  const enemyBoard = ref(createEmptyBoard())
  
  function handleCellClick({ x, y }) {
    console.log('Clicked your cell:', x, y)
  }
  
  function handleEnemyCellClick({ x, y }) {
    console.log('Attack enemy cell:', x, y)
    if(props.yourTurn) {
      // Only now the move should be handled
      emit('move', { x, y })
    }
  }

  const handleLastTurnChange = (newLastTurn, oldLastTurn) => {
    if(newLastTurn === null || oldLastTurn === null) return;
    const x = newLastTurn[0]
    const y = newLastTurn[1]
    const r = newLastTurn[2]
    // X and Y are in reverse for some reason...
    enemyBoard.value[y][x].status = r == 2 ? 'hit' : 'miss'
    console.log('Response for: ', {x, y})
  }

  const handleLastEnemyTurnChange = (newLastEnemyTurn, oldLastEnemyTurn) => {
    if(newLastEnemyTurn === null || oldLastEnemyTurn === null) return;
    const x = newLastEnemyTurn[0]
    const y = newLastEnemyTurn[1]
    const r = newLastEnemyTurn[2]
    if(x === -1 || y === -1 || r === -1) return;
    playerBoard.value[y][x].status = r == 2 ? 'hit' : 'miss'
  }

  watch(
    () => props.lastTurn, handleLastTurnChange
  );

  watch(
    () => props.enemyTurn, handleLastEnemyTurnChange
  );

  </script>
  
  <style scoped>
  .game {
    display: flex;
    justify-content: space-around;
    margin-top: 20px;
    gap: 20rem;
  }

  .info {
    margin-top: 3em;
    font-size: larger;
  }
  </style>
  