<template>
  <v-container fluid class="pa-0" style="max-width: 1200px; margin: 0 auto">
    <SearchBar class="d-flex justify-center mt-5" @search="searchPokemon"></SearchBar>
    <PokemonGrid :pokemons="visiblePokemons"></PokemonGrid>
    <div class="d-flex justify-center mt-3">
      <v-btn @click="loadMorePokemons" v-if="showLoadMore" color="orange" class="text-white">Load More</v-btn>
    </div>
  </v-container>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import SearchBar from '../components/SearchBar.vue'
import PokemonGrid from '../components/PokemonGrid.vue'

const pokemons = ref([])
const searchQuery = ref('')
const visiblePokemonsCount = ref(40)

const fetchPokemons = async () => {
  const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=1100')
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
  visiblePokemonsCount.value = 40
}

const filteredPokemons = computed(() => {
  return pokemons.value.filter((pokemon) =>
    pokemon.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
})

const visiblePokemons = computed(() => {
  return filteredPokemons.value.slice(0, visiblePokemonsCount.value)
})

const showLoadMore = computed(() => {
  return visiblePokemonsCount.value < filteredPokemons.value.length
})

const loadMorePokemons = () => {
  visiblePokemonsCount.value += 40
}
</script>
