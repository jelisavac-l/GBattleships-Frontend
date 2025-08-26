<template>
    <div
      class="cell"
      :class="cellClass"
      :data-x="x"
      :data-y="y"
      @click="$emit('click', { x, y })"
    ></div>
    
  </template>
  
  <script setup>
  import { computed } from 'vue'
  
  const props = defineProps({
    status: String,
    x: Number,
    y: Number,
    isEnemy: Boolean
  })
  
  const cellClass = computed(() => {
    if (props.status === 'hit') return 'hit'
    if (props.status === 'miss') return 'miss'
    if (props.status === 'ship' && !props.isEnemy) return 'ship'
    return ''
  })
  </script>
  
  <style scoped>
  .cell {
    width: 30px;
    height: 30px;
    background-color: lightblue;
    border: 1px solid #3a3a3a;
    cursor: pointer;
  }
  
  .cell.ship {
    background-color: gray;
  }
  
  .cell.hit {
    background-color: red;
  }
  
  .cell.miss {
    background-color: white;
  }
  </style>
  