<script setup>
import { reactive } from 'vue' // convierte un objeto normal en uno reactivo (Vue detecta los cambios)
import Column from './Column.vue' // componente hijo que representa cada columna del tablero (Por hacer, En progreso, Hecho)

// Lista de notas basicas quemadas en codigo, mas adelante seran agregadas por el usuario
//El objeto completo es reactivo, así que cualquier cambio se refleja en la interfaz sin recargar.
const columns = reactive({ 
  todo: [
    { id: 1, text: 'Comprar leche' },
    { id: 2, text: 'Hacer ejercicio' },
  ],
  doing: [
    { id: 3, text: 'Aprender Vue 3' },
  ],
  done: [
    { id: 4, text: 'Leer un libro' },
  ],
})

// Cuando una nota se mueve (drag & drop), se llama esta función.
function updateNotes(columnKey, newNotes) {
  columns[columnKey] = newNotes
}
</script>

<template>
  <!-- creacion de cada contenedor con sus respectivos id para ser usados -->
  <div class="board">
    <Column
      title="Por hacer"
      type="todo"
      :notes="columns.todo"
      @update:notes="updateNotes('todo', $event)"
    />
    <Column
      title="En progreso"
      type="doing"
      :notes="columns.doing"
      @update:notes="updateNotes('doing', $event)"
    />
    <Column
      title="Hecho"
      type="done"
      :notes="columns.done"
      @update:notes="updateNotes('done', $event)"
    />
  </div>
</template>

<style scoped>
.board {
  display: flex;
  gap: 1rem;
  justify-content: center;
  align-items: flex-start;
  flex-wrap: wrap; /* si no caben en una fila (por ejemplo, en móvil), se bajan. */
  text-align: center;
  padding: 1rem;
}
</style>
