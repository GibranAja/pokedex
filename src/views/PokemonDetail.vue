<template>
  <v-container fluid class="pa-0" style="max-width: 1200px; margin: 0 auto">
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
          <v-card-title class="text-h4">{{ pokemon.name }}</v-card-title>
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
                  text-color="white"
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
                  {{ ability.ability.name }}
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
  </v-container>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const pokemon = ref(null)
const locations = ref([])

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

const fetchPokemon = async () => {
  const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${route.params.id}`)
  pokemon.value = await response.json()
  fetchPokemonLocation()
}

const fetchPokemonLocation = async () => {
  const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${route.params.id}/encounters`)
  const data = await response.json()
  locations.value = data.map((encounter) => encounter.location_area.name)
}

onMounted(fetchPokemon)
</script>
