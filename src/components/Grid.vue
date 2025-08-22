<template>
    <div class="grid">
      <Cell
          v-for="(cell, index) in flatBoard"
          :key="index"
          :status="cell.status"
          :x="index % gridSize"
          :y="Math.floor(index / gridSize)"
          :isEnemy="isEnemy"
          @click="onCellClick"
      />
    </div>
  </template>
  
  <script setup>
  import { computed } from 'vue'
  import Cell from './Cell.vue'
  
  const props = defineProps({
    board: Array,
    isEnemy: Boolean
  })
  
  const emit = defineEmits(['cell-clicked'])
  
  const gridSize = 10
  
  const flatBoard = computed(() => props.board.flat())
  
  function onCellClick({ x, y }) {
    emit('cell-clicked', { x, y })
  }
  </script>
  
  <style scoped>
  .grid {
    display: grid;
    grid-template-columns: repeat(10, 30px);
    grid-template-rows: repeat(10, 30px);
    gap: 2px;
  }
  </style>
  