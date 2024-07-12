<template>
  <div v-if="error">{{ error }}</div>
  <div v-else-if="loader"></div>
  <div v-else>
  <div class="background-wrapper">
    <svg class="background-shape top-left" viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
      <path
        fill="#FF0066"
        d="M47.5,-51.2C59.8,-37.7,67.3,-18.9,67.1,-0.2C66.9,18.5,59,37,45.4,50.2C31.8,63.4,15.9,71.3,-1.9,73.2C-19.7,75.1,-39.4,71,-54.4,58.3C-69.4,45.6,-79.7,24.4,-79.9,3C-80.1,-18.3,-70.1,-36.6,-55.3,-50.1C-40.5,-63.6,-20.2,-72.3,-0.6,-71.7C19.1,-71.1,38.2,-61.1,47.5,-51.2Z"
        transform="translate(100 100)"
      />
    </svg>
    <svg
      class="background-shape bottom-right"
      viewBox="0 0 200 200"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path
        fill="#FFD700"
        d="M38.1,-47.8C50.9,-31.5,63.6,-20.9,68.2,-6.7C72.8,7.5,69.3,25.4,59.5,38.9C49.7,52.4,33.6,61.5,15.3,67.2C-2.9,72.9,-23.4,75.2,-39.3,67.4C-55.2,59.6,-66.5,41.6,-71.9,22.2C-77.4,2.8,-77,-17.9,-67.6,-34.6C-58.2,-51.3,-39.8,-63.9,-22.8,-67.7C-5.8,-71.5,9.8,-66.4,38.1,-47.8Z"
        transform="translate(100 100)"
      />
    </svg>
    <v-container fluid class="pa-0" style="max-width: 1200px; margin: 0 auto; position: relative; z-index: 1;">
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
          <v-btn
            @click="loadMorePokemons"
            v-if="showLoadMore"
            color="orange"
            class="text-white load-more-btn"
            size="x-large"
            >Load More</v-btn
          >
        </div>
      </div>
    </v-container>
  </div>
  <FooterComponent />
</div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import SearchBar from '../components/SearchBar.vue'
import PokemonGrid from '../components/PokemonGrid.vue'
import FooterComponent from '@/components/FooterComponent.vue';

const pokemons = ref([])
const searchQuery = ref('')
const visiblePokemonsCount = ref(40)
const loading = ref(true)
const error = ref([])

const fetchPokemons = async () => {
  loading.value = true
  error.value = null
  
  try {
    const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=1100')
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`)
    }
    
    const data = await response.json()
    
    pokemons.value = await Promise.all(
      data.results.map(async (pokemon) => {
        try {
          const res = await fetch(pokemon.url)
          if (!res.ok) {
            throw new Error(`Failed to fetch details for ${pokemon.name}. Status: ${res.status}`)
          }
          const pokemonData = await res.json()
          return pokemonData
        } catch (err) {
          console.error(`Error fetching details for ${pokemon.name}:`, err)
          return null
        }
      })
    )
    
    pokemons.value = pokemons.value.filter(pokemon => pokemon !== null)
    
    console.log(`Successfully fetched ${pokemons.value.length} Pokemon`)
  } catch (err) {
    console.error('Failed to fetch pokemons', err)
    
    if (err instanceof TypeError && err.message.includes('Failed to fetch')) {
      error.value = 'Network error. Please check your internet connection and try again.'
    } else if (err instanceof SyntaxError) {
      error.value = 'Received invalid data from the server. Please try again later.'
    } else if (err.message.includes('HTTP error')) {
      error.value = `Server error (${err.message}). Please try again later.`
    } else {
      error.value = 'An unexpected error occurred. Please try again later.'
    }
    
    console.error('Detailed error information:', {
      message: err.message,
      stack: err.stack,
      type: err.name
    })
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
.background-wrapper {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
}

.background-shape {
  position: fixed;
  z-index: 0;
  opacity: 0.5;
}

.top-left {
  top: -50px;
  left: -50px;
  width: 300px;
  height: 300px;
}

.bottom-right {
  bottom: -50px;
  right: -50px;
  width: 300px;
  height: 300px;
}

@media (max-width: 600px) {
  .top-left, .bottom-right {
    width: 200px;
    height: 200px;
  }
}

@media (min-width: 1200px) {
  .top-left, .bottom-right {
    width: 400px;
    height: 400px;
  }
}

.v-container {
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  padding: 20px;
  margin-top: 20px;
}

.logo {
  max-width: 300px;
  width: 100%;
  height: auto;
}

.load-more-btn {
  margin-top: 40px;
  margin-bottom: 40px;
  z-index: 2;
  position: relative;
}
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
