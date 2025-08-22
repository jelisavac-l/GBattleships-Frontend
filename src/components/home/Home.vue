<template>
    <h1>GBattleships</h1>
    <div class="button-group">
      <button @click="handleHost" class="btn btn-primary">
        Host
      </button>
      <div class="join-group">
        <p>or</p>
        <button @click="handleJoin" class="btn btn-secondary">
          Join
        </button>
        <input 
          type="text" 
          v-model="gameId" 
          placeholder="Enter Game ID" 
          class="form-control"
        />
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  
  const emit = defineEmits(['host-clicked', 'join-clicked']);
  
  const gameId = ref('');
  
  function handleHost() {
    console.log('Host button clicked!');
    emit('host-clicked');
  }
  
  function handleJoin() {
    if (gameId.value.trim() !== '') {
      console.log(`Join button clicked with Game ID: ${gameId.value}`);
      emit('join-clicked', gameId.value);
    } else {
      console.log('Join button clicked, but Game ID is empty!');
    }
  }
  </script>
  
  <style scoped>
  .button-group {
    display: flex;
    justify-content: center;
    gap: 10px;
    align-items: center; 
  }
  
  .join-group {
    display: flex;
    gap: 10px; 
    align-items: center; 
  }
  
  .btn {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
    white-space: nowrap; 
  }
  
  .form-control {
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 16px;
  }
  </style>