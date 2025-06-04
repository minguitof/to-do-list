<script setup>
import { ref, nextTick } from 'vue'
import { defineProps, defineEmits } from 'vue'
import draggable from 'vuedraggable'

const props = defineProps({
  title: String,
  notes: Array,
  type: String,
})

const emit = defineEmits(['update:notes'])

function onUpdateModelValue(newValue) {
  emit('update:notes', newValue)
}

const newNoteText = ref('')
const showInput = ref(false)
const inputRef = ref(null) // referencia al input

function toggleInput() {
  showInput.value = !showInput.value
  if (showInput.value) {
    nextTick(() => {
      inputRef.value?.focus()
    })
  }
}

function addNote() {
  const text = newNoteText.value.trim()
  if (!text) return

  const newNote = {
    id: Date.now(),
    text
  }
  emit('update:notes', [...props.notes, newNote])
  newNoteText.value = ''
  showInput.value = false
}
</script>


<template>
  <!-- Contenedor que nos permite crear las columnas cada una con su respectivo name -->
  <div :class="['column-box', type]"> 
    <!-- permite darle estilo al titulo de cada una de las columnas -->
    <h2>{{ title }}</h2>
    <!-- nos crea un separador con estilo el cual separa el titulo de las notas -->
    <div :class="['column-separator', type]"></div> <!-- la barrita -->

    <!-- 
      Componente draggable (de la librería vuedraggable)
      - Permite arrastrar y soltar notas dentro de la columna
      - También permite moverlas entre columnas (porque comparten el mismo grupo)

      :modelValue="notes"                       -- Lista reactiva que se va a ordenar --
      @update:modelValue="onUpdateModelValue"   -- Evento cuando se arrastra algo --
      :group="{ name: 'notes' }"                -- Agrupación para mover entre columnas --
      item-key="id"                             -- Clave única para cada nota --
    -->
    <draggable
      :modelValue="notes" 
      @update:modelValue="onUpdateModelValue"
      :group="{ name: 'notes' }"
      item-key="id"
      style="min-height: 150px;"
    >
    <!-- Slot que renderiza cada ítem individual -->
      <template #item="{ element }">
        <div style="
          background: white; 
          padding: 12px; 
          margin-bottom: 10px; 
          border-radius: 6px; 
          box-shadow: 0 2px 4px rgba(0,0,0,0.1);
          user-select: none;
          cursor: grab;
        ">
          {{ element.text }} <!-- Muestra el texto de la nota -->
        </div>
      </template>
    </draggable>
    <!-- Botón para mostrar/ocultar el input -->
    <button @click="toggleInput" class="add-note-button">
      ➕ Agregar nota
    </button>

    <!-- Input para escribir nueva nota -->
    <div v-if="showInput" class="add-note-form">
      <input
      ref="inputRef"
      v-model="newNoteText"
      @keyup.enter="addNote"
      type="text"
      placeholder="Escribe una nueva nota"
      />
      <button @click="addNote">Agregar</button>
    </div>
  </div>
</template>

<style scoped>

/* Contenedor visual de cada columna (Todo, Doing, Done) */
.column-box {
  background: #e0e0e0;
  padding: 16px;
  border-radius: 8px;
  width: 280px;
  min-height: 200px;
  flex: 1;
  max-width: 300px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}

/* Si el ancho de la pantalla es menor o igual a 768px (por ejemplo, una tablet o celular), entonces el contenedor .board */
@media (max-width: 768px) {
  .board {
    flex-direction: column;
    align-items: center;
  }
}

/* estilo - Por hacer */
.column-box.todo {
  border-color: #42a5f5;
}

.column-box.todo h2 {
  color: #42a5f5;
  font-style: italic;
  font-size: 2rem;
}

/* estilo -  Haciendo */
.column-box.doing {
  border-color: #ffb300;
}

.column-box.doing h2 {
  color: #ffb300;
  font-style: italic;
  font-size: 2rem;
}

/* estilo - Terminado */
.column-box.done {
  border-color: #66bb6a;
}

.column-box.done h2 {
  color: #66bb6a;
  font-style: italic;
  font-size: 2rem;
}

/* Separador de la columna y titulo - sí la barrita */
.column-separator {
  height: 4px;
  width: 100%;
  margin: 8px 0 16px;
  border-radius: 2px;
}

/* Color por tipo */
.column-separator.todo {
  background-color: #42a5f5;
}

.column-separator.doing {
  background-color: #ffb300;
}

.column-separator.done {
  background-color: #66bb6a;
}

/* Estilo para el boton */
.add-note-button {
  margin-top: 1rem;
  padding: 8px 12px;
  background-color: #ffffff;
  border: 2px dashed #ccc;
  border-radius: 6px;
  font-size: 1rem;
  cursor: pointer;
  width: 100%;
  transition: background 0.2s;
}

.add-note-button:hover {
  background-color: #f0f0f0;
}

/* Estilo para los input del usuario para cada columna */
.add-note-form {
  margin-top: 0.5rem;
  display: flex;
  gap: 0.5rem;
  flex-direction: column;
}

.add-note-form input {
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #ccc;
  font-size: 1rem;
}

.add-note-form button {
  padding: 8px;
  border-radius: 6px;
  background-color: #42a5f5;
  color: white;
  border: none;
  cursor: pointer;
  transition: background 0.2s;
}

.add-note-form button:hover {
  background-color: #1e88e5;
}


</style>
