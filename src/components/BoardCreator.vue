<template>
  <div class="board-creator">
    <div class="controls">
      <button
        v-for="(ship, i) in shipTypes"
        :key="i"
        :disabled="ship.count <= 0"
        @click="selectShip(ship)"
      >
        {{ ship.name }} ({{ ship.count }})
      </button>
      <button @click="clearBoard">Clear</button>
      <button @click="submitBoard" :disabled="!allShipsPlaced">Submit</button>
    </div>

    <Grid :board="board" :isEnemy="false" @cell-clicked="placeShip" />
    <p>Press <b>R</b> to rotate (current: {{ orientation }})</p>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted } from "vue";
import Grid from "./Grid.vue";
const gridSize = 10;

const shipTypes = reactive([
  { name: "Carrier", length: 5, count: 1 },
  { name: "Battleship", length: 4, count: 1 },
  { name: "Cruiser", length: 3, count: 2 },
  { name: "Destroyer", length: 2, count: 1 },
]);

const emit = defineEmits(['submit-board'])

const orientation = ref("horizontal");
const selectedShip = ref(null);

function createEmptyBoard() {
  return Array.from({ length: gridSize }, () =>
    Array.from({ length: gridSize }, () => ({ status: "empty" }))
  );
}

// board must be ref of 2D array
const board = ref(createEmptyBoard());

function selectShip(ship) {
  selectedShip.value = ship;
}

function isValidPlacement(x, y, length, orientation) {
  if (orientation === "horizontal") {
    if (x + length > gridSize) return false;
    for (let i = 0; i < length; i++) {
      if (board.value[y][x + i].status === "ship") return false;
    }
  } else {
    if (y + length > gridSize) return false;
    for (let i = 0; i < length; i++) {
      if (board.value[y + i][x].status === "ship") return false;
    }
  }
  return true;
}

function placeShip({ x, y }) {
  if (!selectedShip.value) return;

  const len = selectedShip.value.length;
  if (!isValidPlacement(x, y, len, orientation.value)) return;

  if (orientation.value === "horizontal") {
    for (let i = 0; i < len; i++) {
      board.value[y][x + i].status = "ship";
    }
  } else {
    for (let i = 0; i < len; i++) {
      board.value[y + i][x].status = "ship";
    }
  }

  selectedShip.value.count -= 1;
  selectedShip.value = null;
}

function clearBoard() {
  board.value = createEmptyBoard();
  shipTypes[0].count = 1;
  shipTypes[1].count = 1;
  shipTypes[2].count = 2;
  shipTypes[3].count = 2;
}

function printBoardMatrix(board) {
  board.forEach(row => {
    // 0 = empty, 1 = ship, 2 = hit, 3 = miss
    const rowStr = row.map(cell => {
      switch (cell.status) {
        case "ship": return 1;
        case "hit": return 2;
        case "miss": return 3;
        case "empty": return 0; 
        default: return 0;
      }
    }).join(" ");
    console.log(rowStr);
  });
}


const allShipsPlaced = computed(() => shipTypes.every((s) => s.count === 0));

function submitBoard() {
  if (!allShipsPlaced.value) {
    alert("You must place all ships first!");
    return;
  }
  console.log("Submitting board:\n", printBoardMatrix(board.value));
  emit('submit-board', board.value)
}

function handleKeydown(e) {
  if (e.key.toLowerCase() === "r") {
    orientation.value =
      orientation.value === "horizontal" ? "vertical" : "horizontal";
  }
}

onMounted(() => {
  window.addEventListener("keydown", handleKeydown);
});
</script>

<style scoped>
.board-creator {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.controls {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}
</style>
