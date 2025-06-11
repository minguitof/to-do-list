<template>
  <div class="settings-wrapper">
    <button @click="toggleMenu" class="gear-button" aria-label="Abrir configuración">
      <!-- SVG del engranaje -->
        <IconSettings />
    </button>

    <!-- Menú desplegable -->
    <div v-if="menuOpen" class="settings-menu">
      <label>
        🌙 Tema oscuro:
        <input type="checkbox" v-model="darkMode" @change="toggleTheme" />
      </label>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import IconSettings from './icons/IconSettings.vue'

const menuOpen = ref(false)
const darkMode = ref(false)

const toggleMenu = () => {
  menuOpen.value = !menuOpen.value
}

const toggleTheme = () => {
  if (darkMode.value) {
    document.documentElement.classList.add('dark')
    localStorage.setItem('theme', 'dark')
  } else {
    document.documentElement.classList.remove('dark')
    localStorage.setItem('theme', 'light')
  }
}

onMounted(() => {
  const saved = localStorage.getItem('theme')
  if (saved === 'dark') {
    darkMode.value = true
    document.documentElement.classList.add('dark')
  }
})
</script>

<style scoped>
@import '../assets/styles/Settings.css';
</style>
