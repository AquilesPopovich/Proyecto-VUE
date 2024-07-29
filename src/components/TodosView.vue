<template>
  <div class="container">
    <nav class="navbar">
      <ul>
        <li><a href="#" @click.prevent="showModal">Crear tabla</a></li>
      </ul>
    </nav>
    <div class="boards">
      <div @drop="onDrop($event, tabla)" @dragover.prevent @dragenter.prevent v-for="tabla in boards" :key="tabla.id" class="board">
        <h2 class="board-title">{{ tabla.name }}</h2>
        <input-new-task :board-id="tabla.id" @on-new-item="handleNewItem" />
        <div class="items">
          <div :class="['item', getImportanceClass(item.importancia)]" draggable="true" @dragstart="startDrag($event, tabla, item)" v-for="item in tabla.items" :key="item.id">
            {{ item.title }}
            <div class="contBtns">
              <select class="btnX selectImportance" v-model="item.importancia" @change="updateTaskImportance(tabla.id, item)">
                <option value="leve">🟡</option>
                <option value="intermedia">🟠</option>
                <option value="urgente">🔴</option>
              </select>
              <button class="btnX" @click="deleteTask(tabla.id, item.id)">x</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <FormCreateBoard ref="modal" @createBoard="addNewBoard" />
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue';
import InputNewTask from './InputNewTask.vue';
import FormCreateBoard from './FormCreateBoard.vue';

const generateUUID = () => crypto.randomUUID();

const boards = reactive([
  {
    id: generateUUID(),
    name: 'Pendientes',
    asociada: '',
    items: [
      { id: generateUUID(), title: 'Crear Proyecto' },
      { id: generateUUID(), title: 'Resolver punto 2' },
      { id: generateUUID(), title: 'Solucionar bug linea 250' }
    ]
  },
  {
    id: generateUUID(),
    name: 'En proceso',
    asociada: '',
    items: [
      { id: generateUUID(), title: 'resolver bug linea 499' },
      { id: generateUUID(), title: 'Resolver punto 1' }
    ]
  }
]);

const modal = ref(null);

const handleNewItem = (boardId, text) => {
  const board = boards.find(b => b.id === boardId);
  if (board && text) {
    board.items.push({ id: generateUUID(), title: text });
  }
};

const showModal = () => {
  if (modal.value) {
    modal.value.openModal();
  }
};

const addNewBoard = ({ name, importance }) => {
  const newBoard = {
    id: generateUUID(),
    name,
    asociada: importance,
    items: []
  };

  boards.forEach(board => {
    board.items.forEach(item => {
      if (item.importancia === importance) {
        newBoard.items.push({ ...item });
      }
    });
  });

  boards.push(newBoard);
};

const updateTaskImportance = (boardId, item) => {
  const board = boards.find(b => b.id === boardId);
  if (board) {
    const currentImportance = item.importancia;

    const itemIndex = board.items.findIndex(i => i.id === item.id);
    if (itemIndex !== -1) {
      board.items[itemIndex] = { ...item };
    }

    boards.forEach(b => {
      if (b.id !== boardId && b.asociada === currentImportance) {
        b.items = b.items.filter(i => i.id !== item.id);
      }
    });

    const associatedBoard = boards.find(b => b.asociada === item.importancia);
    if (associatedBoard) {
      associatedBoard.items.push(item);
    }
  }
};

const startDrag = (event, board, item) => {
  event.dataTransfer.setData('text/plain', JSON.stringify({ boardId: board.id, itemId: item.id }));
};

const onDrop = (event, destBoard) => {
  const data = JSON.parse(event.dataTransfer.getData('text/plain'));
  const sourceBoard = boards.find(b => b.id === data.boardId);
  const itemIndex = sourceBoard.items.findIndex(item => item.id === data.itemId);
  const [item] = sourceBoard.items.splice(itemIndex, 1);
  destBoard.items.push(item);
};

const deleteTask = (boardId, itemId) => {
  const board = boards.find(b => b.id === boardId);
  if (board) {
    board.items = board.items.filter(item => item.id !== itemId);

    boards.forEach(b => {
      if (b.asociada === board.asociada) {
        b.items = b.items.filter(i => i.id !== itemId);
      }
    });
  }
};

const getImportanceClass = (importance) => {
  switch (importance) {
    case 'leve':
      return 'border-leve';
    case 'intermedia':
      return 'border-intermedia';
    case 'urgente':
      return 'border-urgente';
    default:
      return '';
  }
};
</script>

<style scoped>
.container {
  font-family: Arial, sans-serif;
  margin: 0px;
  padding: 20px;
  height: 100vh;
  width: 100vw;
  background-color: #121212;
  overflow-x: hidden;
  overflow-y: hidden;
  color: #e0e0e0;
}

.navbar {
  background-color: #1f1f1f;
  padding: 10px;
  border-bottom: 1px solid #333;
}

.navbar ul {
  list-style: none;
  padding: 0;
}

.navbar li {
  display: inline;
  margin-right: 10px;
}

.navbar a {
  text-decoration: none;
  color: #bb86fc;
  font-weight: bold;
}

.boards {
  display: flex;
  gap: 20px;
  overflow-x: hidden;
  flex-wrap: wrap;
  padding: 10px;
}

.board {
  background: #1f1f1f;
  border: 1px solid #333;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
  width: 300px;
  padding: 15px;
  flex-shrink: 0;
}

.board-title {
  font-size: 18px;
  margin: 0;
  margin-bottom: 5px;
  color: #e0e0e0;
}

.items {
  margin-top: 10px;
}

.item {
  background: #333;
  border: 1px solid #444;
  display: flex;
  justify-content: space-between;
  border-radius: 3px;
  padding: 10px;
  margin-bottom: 10px;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.7);
  color: #e0e0e0;
}

.item:hover {
  background-color: white;
  color: #121212;
  cursor: pointer;
}

.item:last-child {
  margin-bottom: 0;
}

.border-leve {
  border-color: yellow !important;
}

.border-intermedia {
  border-color: orange !important;
}

.border-urgente {
  border-color: red !important;
}

.btnX {
  background: none;
  border: none;
  font-size: 15px;
  display: none;
}

.btnX:hover {
  cursor: pointer;
  border-radius: 3px;
}

.item:hover .btnX {
  display: flex;
}

.contBtns {
  display: flex;
  align-items: center;
}

.selectImportance{
    padding: 0;
    text-align: center;
    font-size: 16px;
    width: 20px;
    height: 20px;
}

.selectImportance option {
  background: transparent;
  border: none;
}

.selectImportance:hover {
  background: #bb86fc;
  cursor: pointer;
}
</style>
