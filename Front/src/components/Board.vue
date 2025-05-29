<script setup>
import { reactive } from 'vue'
import Column from './Column.vue'

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

function updateNotes(columnKey, newNotes) {
  columns[columnKey] = newNotes
}
</script>

<template>
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
  flex-wrap: wrap;
  text-align: center;
  padding: 1rem;
}

</style>
