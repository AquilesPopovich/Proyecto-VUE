<template>
  <div class="practica">
    <h1 v-if="isVisible">{{ message }}</h1>
    <button v-on:click="toggleVisibility">visible</button>
    <u>
      <li v-for="item in arrayPrueba" :key="item">{{item}}</li>
    </u>
  </div>
</template>

<script>
export default {
  name: 'PracticaComponent',
  data() {
    return {
      message: 'Messi el goat',
      isVisible: true,
      arrayPrueba: ["hola", "como", "estas"]
    };
  },
  methods: {
    toggleVisibility(){
      this.isVisible = !this.isVisible
    }
  }
};
</script>

<style>
</style>