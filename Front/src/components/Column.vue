<script setup>
import { ref, nextTick, defineProps, defineEmits } from 'vue'
import draggable from 'vuedraggable' 
import NoteCard from './NoteCard.vue'  // importamos componente de Notas.

// --- Props y emits
const props = defineProps({
  title: String,
  notes: Array,
  type: String,
})

const emit = defineEmits(['update:notes'])

// --- Refs
const newNoteText = ref('') // contenido que el usuario escribe.
const showInput = ref(false) 
const inputRef = ref(null) // referencia al input para poder hacerle focus.

const editingId = ref(null) // guarda el ID de la nota que se está editando.
const editText = ref('') // texto temporal que se está editando.

const activeMenuId = ref(null) // Menú contextual (tres puntos)

// controla si el input está visible.
function toggleInput() {
  showInput.value = !showInput.value
  if (showInput.value) {
   nextTick(() => inputRef.value?.focus())
  }
}

// Función que emite una nueva lista de notas.
function onUpdateModelValue(newValue) {
  emit('update:notes', newValue)
}

// Crea una nueva nota con un id único (timestamp) y la agrega a la lista.
function addNote() {
  const text = newNoteText.value.trim()
  if (!text) return

  const newNote = { id: Date.now(), text }
  emit('update:notes', [...props.notes, newNote])
  newNoteText.value = ''
  showInput.value = false
}

// Aquí van funciones relacionadas a la edición y menú
function toggleMenu(id) {
  activeMenuId.value = activeMenuId.value === id ? null : id
}

// Maneja qué menú (de los 3 puntos) está abierto actualmente.
function closeMenu(id) {
  if (activeMenuId.value === id) activeMenuId.value = null
}

// Comienza la edición: muestra el input con el texto original.
function startEdit(note) {
  editingId.value = note.id
  editText.value = note.text
  activeMenuId.value = null
}

// Guarda los cambios editados y actualiza la lista.
function saveEdit(note) {
  const updatedText = editText.value.trim()
  if (!updatedText) return
  const updatedNotes = props.notes.map(n =>
    n.id === note.id ? { ...n, text: updatedText } : n
  )
  emit('update:notes', updatedNotes)
  editingId.value = null
}

// Cancela la edición sin guardar.
function cancelEdit() {
  editingId.value = null
}

// Muestra confirmación antes de eliminar la nota seleccionada.
function confirmDelete(note) {
  activeMenuId.value = null
  const confirmed = confirm('¿Estás seguro de eliminar esta nota?')
  if (!confirmed) return
  const updatedNotes = props.notes.filter(n => n.id !== note.id)
  emit('update:notes', updatedNotes)
}

// Escucha si surgen cambios en el componente NoteCard.
function updateEditText(value) {
  editText.value = value
}

</script>

<template>
  <div :class="['column-box', type]">
    <h2>{{ title }}</h2>
    <div :class="['column-separator', type]"></div>

    <draggable
      :modelValue="notes"
      @update:modelValue="onUpdateModelValue"
      :group="{ name: 'notes' }"
      item-key="id"
      style="min-height: 150px;"
    >
      <template #item="{ element }">
        <NoteCard
          :note="element"
          :editingId="editingId"
          :editText="editText"
          :activeMenuId="activeMenuId"
          @toggleMenu="toggleMenu"
          @closeMenu="closeMenu"
          @startEdit="startEdit"
          @saveEdit="saveEdit"
          @cancelEdit="cancelEdit"
          @confirmDelete="confirmDelete"
          @updateEditText="updateEditText"
        />
      </template>
    </draggable>

    <button @click="toggleInput" class="add-note-button">➕ Agregar nota</button>

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

.board {
  display: flex;
  gap: 16px;
  padding: 16px;
  justify-content: center;
}

@media (max-width: 768px) {
  .board {
    flex-direction: column;
    align-items: center;
  }
}

.column-box.todo {
  border-color: #42a5f5;
}
.column-box.todo h2 {
  color: #42a5f5;
  font-style: italic;
  font-size: 2rem;
}

.column-box.doing {
  border-color: #ffb300;
}
.column-box.doing h2 {
  color: #ffb300;
  font-style: italic;
  font-size: 2rem;
}

.column-box.done {
  border-color: #66bb6a;
}
.column-box.done h2 {
  color: #66bb6a;
  font-style: italic;
  font-size: 2rem;
}

.column-separator {
  height: 4px;
  width: 100%;
  margin: 8px 0 16px;
  border-radius: 2px;
}

.column-separator.todo {
  background-color: #42a5f5;
}

.column-separator.doing {
  background-color: #ffb300;
}

.column-separator.done {
  background-color: #66bb6a;
}

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

