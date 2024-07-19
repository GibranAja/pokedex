<template>
  <v-card
    v-if="pokemon"
    :color="getTypeColor(pokemon.types[0]?.type.name)"
    @click="$router.push(`/pokemon/${pokemon.id}`)"
    class="ma-2"
    elevation="2"
    height="100%"
  >
    <v-img :src="pokemon.sprites.front_default" height="150"></v-img>
    <v-card-title>{{ pokemon.name }}</v-card-title>
    <v-card-subtitle class="text-center"
      >#{{ pokemon.id.toString().padStart(3, '0') }}</v-card-subtitle
    >
  </v-card>
</template>

<script setup>
import { defineProps } from 'vue'
import { useRouter } from 'vue-router'

defineProps(['pokemon'])
useRouter()

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

const getTypeColor = (type) => typeColors[type] || '#A8A878'
</script>
