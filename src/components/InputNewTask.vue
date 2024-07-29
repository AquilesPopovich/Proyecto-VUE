<template>
  <form @submit.prevent="handleSubmit" class="task-form">
    <input type="text" v-model="text" placeholder="Añadir nueva tarea..." class="task-input" />
    <button type="submit" class="submit-button">Añadir</button>
  </form>
</template>

<script setup>
import { ref, defineEmits, defineProps } from 'vue';

const text = ref('');
const emits = defineEmits(['onNewItem']);
const props = defineProps({
  boardId: String
});

const handleSubmit = () => {
  if (text.value.trim() !== '') {
    emits('onNewItem', props.boardId, text.value);
    text.value = '';
  }
};
</script>

<style scoped>
.task-form {
  display: flex;
  gap: 10px;
}

.task-input {
  flex: 1;
  padding: 10px;
  border: 1px solid #333;
  border-radius: 3px;
  background-color: #1f1f1f; /* Fondo oscuro para el campo de entrada */
  color: #e0e0e0; /* Texto claro para el campo de entrada */
  font-size: 14px;
}

.submit-button {
  background-color: #bb86fc; /* Color claro para el botón */
  color: #121212; /* Texto oscuro para el botón */
  border: none;
  border-radius: 3px;
  padding: 10px 15px;
  cursor: pointer;
  font-size: 14px;
}

.submit-button:hover {
  background-color: #9b4dff; /* Color más oscuro para el botón al pasar el mouse */
}
</style>
