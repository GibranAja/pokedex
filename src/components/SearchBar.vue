<template>
  <v-container>
    <v-text-field
      ref="searchInput"
      v-model="searchQuery"
      label="Search Pokemon by name, ID, or element"
      @input="emitSearch"
      prepend-inner-icon="mdi-magnify"
      class="mt-5"
      style="max-width: 300px"
      outlined
      dense
    ></v-text-field>
  </v-container>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const searchQuery = ref('')
const searchInput = ref(null)

const emit = defineEmits(['search'])

const emitSearch = () => {
  emit('search', searchQuery.value)
}

const handleKeyDown = (event) => {
  if (event.ctrlKey && event.key === '/') {
    event.preventDefault()
    searchInput.value.focus()
  }
}

onMounted(() => {
  window.addEventListener('keydown', handleKeyDown)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown)
})
</script>
