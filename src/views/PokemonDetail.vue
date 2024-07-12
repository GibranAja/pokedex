<template>
  <v-container fluid class="pa-0" style="max-width: 1200px; margin: 0 auto">
    <v-btn
      color="secondary"
      @click="$router.go(-1)"
      prepend-icon="mdi-arrow-left"
      size="large"
      class="my-6 ml-4"
      variant="elevate"
      >Back</v-btn
    >
    <v-card v-if="pokemon" :color="getTypeColor(pokemon.types[0].type.name)" class="ma-4">
      <v-row no-gutters>
        <v-col cols="12" md="4">
          <v-img
            :src="pokemon.sprites.other['official-artwork'].front_default"
            height="300"
            contain
          ></v-img>
        </v-col>
        <v-col cols="12" md="8">
          <v-card-title class="text-h4">{{ capitalizeFirstLetter(pokemon.name) }}</v-card-title>
          <v-card-subtitle>#{{ pokemon.id.toString().padStart(3, '0') }}</v-card-subtitle>
          <v-card-text>
            <v-row>
              <v-col cols="6">
                <p><strong>Height:</strong> {{ pokemon.height / 10 }} m</p>
                <p><strong>Weight:</strong> {{ pokemon.weight / 10 }} kg</p>
                <p><strong>Base Experience:</strong> {{ pokemon.base_experience }}</p>
              </v-col>
              <v-col cols="6">
                <p><strong>Types:</strong></p>
                <v-chip
                  v-for="type in pokemon.types"
                  :key="type.type.name"
                  :color="getTypeColor(type.type.name)"
                  class="ma-1"
                  :style="{ color: getTextColor(getTypeColor(type.type.name)) + ' !important' }"
                >
                  {{ type.type.name }}
                </v-chip>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <p><strong>Abilities:</strong></p>
                <v-chip
                  v-for="ability in pokemon.abilities"
                  :key="ability.ability.name"
                  class="ma-1"
                >
                  {{ capitalizeFirstLetter(ability.ability.name) }}
                </v-chip>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <p><strong>Stats:</strong></p>
                <v-progress-linear
                  v-for="stat in pokemon.stats"
                  :key="stat.stat.name"
                  :model-value="stat.base_stat"
                  :color="getStatColor(stat.stat.name)"
                  height="25"
                  style="border-radius: 15px"
                  striped
                >
                  <template v-slot:default="{ value }">
                    <strong>{{ stat.stat.name }}: {{ value }}</strong>
                  </template>
                </v-progress-linear>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <p><strong>Locations:</strong></p>
                <ul>
                  <li v-for="location in locations" :key="location">{{ location }}</li>
                </ul>
              </v-col>
            </v-row>
          </v-card-text>
        </v-col>
      </v-row>
    </v-card>
    <div v-else class="d-flex justify-center mt-16">
      <div class="loader"></div>
    </div>
  </v-container>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const pokemon = ref(null)
const locations = ref([])
const loading = ref(true)

const typeColors = {
  normal: '#A8A878',
  fire: '#F08030',
  water: '#6890F0',
  grass: '#78C850',
  electric: '#F8D030',
  ice: '#98D8D8',
  fighting: '#C03028',
  poison: '#A040A0',
  ground: '#E0C068',
  flying: '#A890F0',
  psychic: '#F85888',
  bug: '#A8B820',
  rock: '#B8A038',
  ghost: '#705898',
  dark: '#705848',
  dragon: '#7038F8',
  steel: '#B8B8D0',
  fairy: '#F0B6BC'
}

const statColors = {
  hp: '#FF0000',
  attack: '#F08030',
  defense: '#F8D030',
  'special-attack': '#6890F0',
  'special-defense': '#78C850',
  speed: '#F85888'
}

const getTypeColor = (type) => typeColors[type] || '#A8A878'
const getStatColor = (stat) => statColors[stat] || '#A8A878'

const getTextColor = (backgroundColor) => {
  const hex = backgroundColor.replace('#', '');
  const r = parseInt(hex.substr(0, 2), 16);
  const g = parseInt(hex.substr(2, 2), 16);
  const b = parseInt(hex.substr(4, 2), 16);
  
  const brightness = (r * 299 + g * 587 + b * 114) / 1000;
  
  return brightness > 128 ? 'white' : 'black';
}

const capitalizeFirstLetter = (string) => {
  return string.charAt(0).toUpperCase() + string.slice(1)
}

const fetchPokemon = async () => {
  try {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${route.params.id}`)
    pokemon.value = await response.json()
    fetchPokemonLocation()
  } catch (error) {
    console.error('Failed to fetch pokemon data', error)
  } finally {
    loading.value = false
  }
}

const fetchPokemonLocation = async () => {
  const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${route.params.id}/encounters`)
  const data = await response.json()
  locations.value = data.map((encounter) => encounter.location_area.name)
}

onMounted(fetchPokemon)
</script>

<style scoped>
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
