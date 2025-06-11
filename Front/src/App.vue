<template>
    <!-- div que permite jugar con el estilo del titulo -->
    <h1 class="rainbow-text">Lista To-Do-List</h1> 
      
    <!-- contenedor principal que define cómo se estructura la página -->
    <div class="main-container">
      <Settings />
      <!-- subcontenedor dentro de main-container, donde se posicionan los elementos de forma horizontal o vertical (según el caso). Aquí aloja al componente <Board /> -->
      <div class="column-container">
        <Board />
      </div>
    </div> 
      
</template>

<script>
import Board from './components/Board.vue';
// import IconSettings from './components/icons/IconSettings.vue';
import Settings from './components/Settings.vue';

export default {
  components: {
    Board,
    Settings
  }
};
</script>

<style>
@import '@/assets/styles/App.css';
</style>
