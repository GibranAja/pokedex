<template>
  <v-container fluid class="pa-0" style="max-width: 1200px; margin: 0 auto">
    <SearchBar @search="searchPokemon"></SearchBar>
    <PokemonGrid :pokemons="filteredPokemons"></PokemonGrid>
  </v-container>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import SearchBar from '../components/SearchBar.vue'
import PokemonGrid from '../components/PokemonGrid.vue'

const pokemons = ref([])
const searchQuery = ref('')

const fetchPokemons = async () => {
  const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=300')
  const data = await response.json()
  pokemons.value = await Promise.all(
    data.results.map(async (pokemon) => {
      const res = await fetch(pokemon.url)
      return res.json()
    })
  )
}

onMounted(fetchPokemons)

const searchPokemon = (query) => {
  searchQuery.value = query
}

const filteredPokemons = computed(() => {
  return pokemons.value.filter((pokemon) =>
    pokemon.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
})
</script>
