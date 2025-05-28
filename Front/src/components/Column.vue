<template>

  <div class="column-box" :class="columnKey">

  <div class="column-box">
    <h2>{{ title }}</h2>
    <ul class="note-list">
      <li v-for="note in notes" :key="note.id" class="note">
        {{ note.text }}
      </li>
    </ul>
  </div>

  </div>
</template>


<script>
export default {
  name: 'Column',
  props: {
  title: {
    type: String,
    required: true
  },
  notes: {
    type: Array,
    default: () => []
  },
  columnKey: {
    type: String,
    required: true
  }
}
};
</script>
<style scoped>

.column-box {
  flex: 1;
  min-width: 250px;
  max-width: 300px;
  border: 2px solid #ccc; 
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


</style>
