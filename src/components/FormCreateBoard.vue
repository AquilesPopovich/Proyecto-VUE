<template>
  <div v-if="isVisible" class="modal-overlay">
    <div class="modal-content">
      <form @submit.prevent="createBoard">
        <label for="boardName" class="modal-label">Nombre:</label>
        <input v-model="boardName" id="boardName" type="text" class="modal-input" />
        <label for="importance" class="modal-label">Asociar con tareas:</label>
        <select v-model="selectedImportance" id="importance" class="modal-input">
          <option value="leve">🟡 Leves</option>
          <option value="intermedia">🟠 Intermedias</option>
          <option value="urgente">🔴 Urgentes</option>
        </select>
        <div class="modal-buttons">
          <button type="submit" class="modal-button create-button">Crear tabla</button>
          <button type="button" @click="closeModal" class="modal-button cancel-button">Cancelar</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, defineExpose, defineEmits } from 'vue';

const isVisible = ref(false);
const boardName = ref('');
const selectedImportance = ref('');
const emit = defineEmits(['createBoard']);

const openModal = () => {
  isVisible.value = true;
};

const closeModal = () => {
  isVisible.value = false;
  boardName.value = ''; 
  selectedImportance.value = '';
};

const createBoard = () => {
  if (boardName.value.trim()) {
    emit('createBoard', { name: boardName.value, importance: selectedImportance.value });
    closeModal();
  }
};

defineExpose({ openModal });
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: #1f1f1f;
  border-radius: 8px;
  padding: 20px;
  max-width: 500px;
  width: 100%;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
  color: #e0e0e0;
  animation: fadeIn 0.3s ease-in-out;
}

.modal-label {
  display: block;
  margin-bottom: 10px;
  font-size: 16px;
  color: #bb86fc;
}

.modal-input {
  width: calc(100% - 10px);
  padding: 10px;
  margin-bottom: 20px;
  border: 1px solid #333;
  border-radius: 4px;
  background: #2c2c2c;
  color: #e0e0e0;
  font-size: 16px;
}

.modal-buttons {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}

.modal-button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.create-button {
  background: #bb86fc;
  color: #000000;
}

.create-button:hover {
  background: #3700b3;
}

.cancel-button {
  background: #b00020;
  color: #ffffff;
}

.cancel-button:hover {
  background: #7f0000;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>
