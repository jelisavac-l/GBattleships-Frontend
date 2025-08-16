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
  import { ref, defineEmits } from 'vue';
  
  // Define the custom events this component can emit
  const emit = defineEmits(['host-clicked', 'join-clicked']);
  
  // A reactive property to store the game ID input
  const gameId = ref('');
  
  // Method to handle the Host button click
  function handleHost() {
    console.log('Host button clicked!');
    emit('host-clicked');
  }
  
  // Method to handle the Join button click
  function handleJoin() {
    // Check if the gameId input is not empty
    if (gameId.value.trim() !== '') {
      console.log(`Join button clicked with Game ID: ${gameId.value}`);
      // Emit the event with the gameId value as a payload
      emit('join-clicked', gameId.value);
    } else {
      console.log('Join button clicked, but Game ID is empty!');
      // You could also emit an error event or show a message to the user
    }
  }
  </script>
  
  <style scoped>
  .button-group {
    display: flex;
    justify-content: center;
    gap: 10px; /* Space between "Host" button and "Join" group */
    align-items: center; /* Vertically aligns the items in the group */
  }
  
  .join-group {
    display: flex;
    gap: 10px; /* Space between input and "Join" button */
    align-items: center; /* Vertically aligns the input and button */
  }
  
  /* Optional: Basic styling if not using a framework */
  .btn {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
    white-space: nowrap; /* Prevents button text from wrapping */
  }
  
  .form-control {
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 16px;
  }
  </style>