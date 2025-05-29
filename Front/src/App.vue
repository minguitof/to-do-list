<template>
  <div class="main-container">
    <div class="title-center">
        <h1 class="rainbow-text">Lista To-Do-List</h1>
    </div>

    <div class="column-container">
    <Board />
    </div>
  </div>
</template>

<script>
import Board from './components/Board.vue';

export default {
  components: {
    Board
  }
};
</script>

<style scoped>

.main-container {
  display: flex;
  flex-direction: column; /* Coloca hijos en vertical */
  height: 100vh;
  box-sizing: border-box;
  color: rgb(46, 24, 24);
}

.title-center {
  text-align: center;
  margin-top: 1rem;
}

.column-container {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 1rem;
  gap: 1rem;
  flex: 1; /* Para que ocupe el resto del espacio */
  color: #333;
}

html, body, #app {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  box-sizing: border-box;
}

/* Degradado para el titulo */

.rainbow-text {
  font-weight: bold;
  font-size: 4rem;
  background: linear-gradient(
    90deg,
    red,
    orange,
    yellow,
    green,
    blue,
    indigo,
    violet
  );
  background-size: 400%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: rainbow-text 5s linear infinite;
  text-align: center;   
}

@keyframes rainbow-text {
  0% {
    background-position: 0%;
  }
  100% {
    background-position: 100%;
  }
}


</style>
