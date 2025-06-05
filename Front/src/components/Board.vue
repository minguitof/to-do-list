<script setup>
import { reactive, watch, onMounted } from 'vue'
import Column from './Column.vue'

const STORAGE_KEY = 'kanban-notes' //Se define una clave constante para identificar tus datos dentro del localStorage

// Cargar desde localStorage (o usar datos por defecto)
const storedData = localStorage.getItem(STORAGE_KEY)
const columns = reactive(
  storedData
    ? JSON.parse(storedData)
    : {
        todo: [
          { id: 1, text: 'Comprar Portatil' },
          { id: 2, text: 'Hacer ejercicio' },
        ],
        doing: [
          { id: 3, text: 'Aprender Vue 3' },
        ],
        done: [
          { id: 4, text: 'Leer un libro' },
        ],
      }
)

// Observar los cambios en columns y guardarlos automáticamente
watch(
  () => columns,
  (newVal) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(newVal))
  },
  { deep: true }
)

// Actualiza la columna con nuevas notas
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
