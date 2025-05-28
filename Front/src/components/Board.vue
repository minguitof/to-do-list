<script setup>
import { reactive } from 'vue'
import Column from './Column.vue'

// Estado con las columnas y sus notas
const columns = reactive({
  todo: [
    { id: 1, text: 'Aprender Vue' },
    { id: 2, text: 'Hacer ejercicio' }
  ],
  doing: [
    { id: 3, text: 'Estudiar lógica' }
  ],
  done: [
    { id: 4, text: 'Leer un libro' }
  ]
})

// Función para mover notas entre columnas (más adelante puedes agregar lógica drag & drop)
function moveNote(noteId, from, to) {
  const index = columns[from].findIndex(note => note.id === noteId)
  if (index !== -1) {
    const [note] = columns[from].splice(index, 1)
    columns[to].push(note)
  }
}
</script>

<template>
  <div class="board">
    <Column
      title="Por hacer"
      :notes="columns.todo"
      @moveNote="moveNote"
      columnKey="todo"
    />
    <Column
      title="Haciendo"
      :notes="columns.doing"
      @moveNote="moveNote"
      columnKey="doing"
    />
    <Column
      title="Terminado"
      :notes="columns.done"
      @moveNote="moveNote"
      columnKey="done"
    />
  </div>
</template>

<style scoped>
.board {
  display: flex;
  gap: 1rem;
  justify-content: center;
  align-items: flex-start;
  flex-wrap: wrap;
  text-align: center;
  padding: 1rem;
}

</style>
