<script setup>
import { ref, nextTick, defineProps, defineEmits, onMounted, onBeforeUnmount } from 'vue'
import draggable from 'vuedraggable'
import IconThreePoints from './icons/IconThreePoints.vue'

// --- Refs
const newNoteText = ref('') // contenido que el usuario escribe.
const showInput = ref(false) 
const inputRef = ref(null) // referencia al input para poder hacerle focus.
const editingId = ref(null) // guarda el ID de la nota que se está editando.
const editText = ref('') // texto temporal que se está editando.
const activeMenuId = ref(null) // Menú contextual (tres puntos)

// --- Props y emits
const props = defineProps({
  title: String, //título del bloque de notas.
  notes: Array, // lista de notas, tipo array.
  type: String,
})

const emit = defineEmits(['update:notes']) // útil para actualizar la lista desde el padre.

// controla si el input está visible.
function toggleInput() {
  showInput.value = !showInput.value
  if (showInput.value) {
    nextTick(() => {
      inputRef.value?.focus()
    })
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

  const newNote = {
    id: Date.now(),
    text
  }
  emit('update:notes', [...props.notes, newNote])
  newNoteText.value = ''
  showInput.value = false
}

// Menú contextual (tres puntos)
// Maneja qué menú (de los 3 puntos) está abierto actualmente.
function toggleMenu(id) {
  activeMenuId.value = activeMenuId.value === id ? null : id
}

// Maneja qué menú (de los 3 puntos) está abierto actualmente.
function closeMenu(id) {
  if (activeMenuId.value === id) {
    activeMenuId.value = null
  }
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

// Si haces clic fuera de una tarjeta, se cierra el menú abierto.
function handleClickOutside(event) {
  if (!event.target.closest('.note-card')) {
    activeMenuId.value = null
  }
}

// Se añade un listener al montar el componente y se elimina al desmontarlo para evitar fugas de memoria.
onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onBeforeUnmount(() => {
  document.removeEventListener('click', handleClickOutside)
})

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
      style="min-height: 150px;"                -- establece una altura mínima para el elemento --
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
        <div class="note-card" @click.outside="closeMenu(element.id)">
          <div v-if="editingId === element.id">
            <input
              v-model="editText"
              @keyup.enter="saveEdit(element)"
              @blur="cancelEdit"
              class="note-input"
              autofocus
            />
          </div>
          <div v-else class="note-content">
            <span>{{ element.text }}</span>
             <button class="menu-button" @click.stop="toggleMenu(element.id)">
               <IconThreePoints />
              </button>

            <div v-if="activeMenuId === element.id" class="menu-dropdown">
              <button @click="startEdit(element)">✏️ Editar</button>
              <button @click="confirmDelete(element)">🗑️ Eliminar</button>
            </div>
          </div>
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
  <!-- Prueba de íconos funcionando 100% -->
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

.note-card {
  background: white;
  padding: 12px;
  margin-bottom: 10px;
  border-radius: 6px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  position: relative;
  display: flex;
  justify-content: space-between;
  align-items: center;
  cursor: grab;
}

/* Cuando la estés agarrando (dragging) */
.note-card:active {
  cursor: grabbing;
}

.menu-button {
  position: absolute;
  top: 8px;
  right: 8px;
  background: transparent;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  user-select: none;
  padding: 0;
}

.menu-dropdown {
  position: absolute;
  top: 36px;
  right: 12px;
  background: white;
  box-shadow: 0 2px 8px rgba(0,0,0,0.2);
  border-radius: 6px;
  display: flex;
  flex-direction: column;
  width: 100px;
  z-index: 10;
}

.menu-dropdown button {
  border: none;
  background: none;
  padding: 8px 12px;
  text-align: left;
  cursor: pointer;
  font-size: 0.9rem;
}

.menu-dropdown button:hover {
  background-color: #eee;
}

.note-input {
  width: 100%;
  padding: 6px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

</style>
