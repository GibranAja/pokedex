<template>
  <v-container>
    <v-textfield
    v-model="searchQuery"
    label="Search Pokemon"
    @input="$emit('search', 'searchQuery')"
    prepend-inner-icon="mdi-magnify"
    class="mt-5"
    style="max-width: 250px"
    outlined
    dense
    ></v-textfield>
  </v-container>
</template>

<script setup>
import { ref } from 'vue'

const searchQuery = ref('')
</script>