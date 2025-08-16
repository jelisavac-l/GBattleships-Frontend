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
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import Grid from './Grid.vue'
  
  const gridSize = 10
  
  // Initialize empty 10x10 boards
  const createEmptyBoard = () =>
    Array.from({ length: gridSize }, () =>
      Array.from({ length: gridSize }, () => ({
        status: 'empty', // empty, ship, hit, miss
      }))
    )
  
  const playerBoard = ref(createEmptyBoard())
  const enemyBoard = ref(createEmptyBoard())
  
  function handleCellClick({ x, y }) {
    console.log('Clicked your cell:', x, y)
    // For testing: toggle a ship
    const cell = playerBoard.value[y][x]
    cell.status = cell.status === 'ship' ? 'empty' : 'ship'
  }
  
  function handleEnemyCellClick({ x, y }) {
    console.log('Attack enemy cell:', x, y)
    const cell = enemyBoard.value[y][x]
    if (cell.status === 'empty') {
      cell.status = 'miss'
    } else if (cell.status === 'ship') {
      cell.status = 'hit'
    }
  }
  </script>
  
  <style scoped>
  .game {
    display: flex;
    justify-content: space-around;
    margin-top: 20px;
    gap: 20rem;
  }
  </style>
  