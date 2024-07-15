<template>
  <v-container>
    <v-text-field
      ref="searchInput"
      v-model="searchQuery"
      label="Search Pokemon"
      @input="$emit('search', searchQuery)"
      prepend-inner-icon="mdi-magnify"
      class="mt-5"
      style="max-width: 250px"
      outlined
      dense
    ></v-text-field>
  </v-container>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const searchQuery = ref('')
const searchInput = ref(null)

defineEmits(['search'])

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