<script setup>
import { defineProps, defineEmits } from 'vue'
import draggable from 'vuedraggable'

const props = defineProps({
  title: String,
  notes: Array,
  type: String, // "todo", "doing", "done"
})

const emit = defineEmits(['update:notes'])

function onUpdateModelValue(newValue) {
  emit('update:notes', newValue)
}


</script>

<template>
  <div :class="['column-box', type]">
    <h2>{{ title }}</h2>
    <div :class="['column-separator', type]"></div> <!-- la barrita -->

    <draggable
      :modelValue="notes"
      @update:modelValue="onUpdateModelValue"
      :group="{ name: 'notes' }"
      item-key="id"
      style="min-height: 150px;"
    >
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
          {{ element.text }}
        </div>
      </template>
    </draggable>
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

.note-list {
  list-style: none;
  padding: 0;
  margin-top: 1rem;
  text-align: left;
}

.note {
  background-color: #f0f4ff;
  border: 1px solid #ccc;
  border-left: 4px solid #3f51b5;
  border-radius: 6px;
  padding: 0.75rem;
  margin-bottom: 0.75rem;
  font-size: 14px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}

@media (max-width: 768px) {
  .board {
    flex-direction: column;
    align-items: center;
  }
}

/* Por hacer */
.column-box.todo {
  border-color: #42a5f5;
}

.column-box.todo h2 {
  color: #42a5f5;
  font-style: italic;
  font-size: 2rem;
}

/* Haciendo */
.column-box.doing {
  border-color: #ffb300;
}

.column-box.doing h2 {
  color: #ffb300;
  font-style: italic;
  font-size: 2rem;
}

/* Terminado */
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

</style>
