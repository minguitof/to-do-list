<script setup>
import { ref } from 'vue'
import IconThreePoints from './icons/IconThreePoints.vue'

const props = defineProps({
  note: Object,
  editingId: [Number, null],
  editText: String,
  activeMenuId: [Number, null],
})

const emit = defineEmits(['startEdit', 'saveEdit', 'cancelEdit', 'toggleMenu', 'closeMenu', 'confirmDelete', 'updateEditText'])

function onInputChange(event) {
  emit('updateEditText', event.target.value)
}

</script>

<template>
  <div class="note-card" @click.outside="() => emit('closeMenu', props.note.id)">
    <div v-if="editingId === note.id">
        <input
          :value="editText"
          @input="onInputChange"
          @keyup.enter="() => emit('saveEdit', note)"
          @blur="() => emit('cancelEdit')"
          class="note-input"
          autofocus
        />
    </div>
    <div v-else class="note-content">
      <span>{{ note.text }}</span>
      <button class="menu-button" @click.stop="() => emit('toggleMenu', note.id)">
        <IconThreePoints />
      </button>

      <div v-if="activeMenuId === note.id" class="menu-dropdown">
        <button @click="() => emit('startEdit', note)">✏️ Editar</button>
        <button @click="() => emit('confirmDelete', note)">🗑️ Eliminar</button>
      </div>
    </div>
  </div>
</template>

<style scoped>

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


