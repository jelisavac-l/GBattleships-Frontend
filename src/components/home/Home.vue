<template>
    <h1 style="font-weight: 500;">
      <span style="color: dodgerblue;">g</span>
      <span style="font-weight: bold;">Battleships</span>
    </h1>
    <p style="margin-top: -2em;">
      This project has been developed as part of the coursework for the subject Computer Networks and Telecommunications.
    </p>
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
    <footer class="authors">
      <p>(C) 2025. <a href="https://github.com/jelisavac-l">Jelisavac Luka (2022/0554)</a> & <a href="https://github.com/cid-39">Janjusevic Vuk (2022/0225)</a></p>
    </footer>
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
  .authors{
    display: flex;
    justify-content: space-evenly;
      position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    text-align: center;
    padding: 1px;
  }

  .authors a{
    color: dodgerblue;
  }

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

  .btn:hover {
    border-radius: 10px;
    color: dodgerblue;
    transition: ease-in-out 120ms;
  }
  
  .form-control {
    font-family: Minecraft;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 16px;
    border: solid 1px;
  }
  </style>