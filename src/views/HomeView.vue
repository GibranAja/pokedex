<template>
  <v-container fluid class="pa-0" style="max-width: 1200px; margin: 0 auto">
    <div class="d-flex justify-center mt-7">
      <img src="../assets/Pokemon-removebg-preview.png" alt="Pokemon Logo" class="logo" />
    </div>
    <SearchBar class="d-flex justify-center mt-5" @search="searchPokemon"></SearchBar>
    <div v-if="loading" class="d-flex justify-center mt-5">
      <div class="loader"></div>
    </div>
    <div v-else>
      <PokemonGrid :pokemons="visiblePokemons"></PokemonGrid>
      <div class="d-flex justify-center mb-6 mt-4">
        <v-btn @click="loadMorePokemons" v-if="showLoadMore" color="orange" class="text-white" size="x-large"
          >Load More</v-btn
        >
      </div>
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
const loading = ref(true)

const fetchPokemons = async () => {
  try {
    const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=1100')
    const data = await response.json()
    pokemons.value = await Promise.all(
      data.results.map(async (pokemon) => {
        const res = await fetch(pokemon.url)
        return res.json()
      })
    )
  } catch (error) {
    console.error('Failed to fetch pokemons', error)
  } finally {
    loading.value = false
  }
}

onMounted(fetchPokemons)

const searchPokemon = (query) => {
  searchQuery.value = query
  visiblePokemonsCount.value = 40
}

const capitalizeFirstLetter = (string) => {
  return string.charAt(0).toUpperCase() + string.slice(1)
}

const filteredPokemons = computed(() => {
  return pokemons.value
    .filter((pokemon) => pokemon.name.toLowerCase().includes(searchQuery.value.toLowerCase()))
    .map((pokemon) => ({
      ...pokemon,
      name: capitalizeFirstLetter(pokemon.name)
    }))
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

<style scoped>
/* HTML: <div class="loader"></div> */
.loader {
  width: 124px;
  height: 24px;
  -webkit-mask:
    conic-gradient(from 135deg at top, #0000, #000 0.5deg 90deg, #0000 90.5deg) 0 0,
    conic-gradient(from -45deg at bottom, #0000, #000 0.5deg 90deg, #0000 90.5deg) 0 100%;
  -webkit-mask-size: 25% 50%;
  -webkit-mask-repeat: repeat-x;
  background: linear-gradient(#25b09b 0 0) left/0% 100% no-repeat #ddd;
  animation: l13 2s infinite linear;
}
@keyframes l13 {
  100% {
    background-size: 100% 100%;
  }
}
</style>
