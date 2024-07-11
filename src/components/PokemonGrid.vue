<template>
    <v-container fluid>
      <v-row>
        <v-col v-for="pokemon in pokemons" :key="pokemon.id" cols="12" sm="4" md="4">
          <PokemonCard :pokemon="pokemon" />
        </v-col>
      </v-row>
    </v-container>
  </template>
  
  <script setup>
  import { defineProps } from 'vue'
  import PokemonCard from './PokemonCard.vue'
  
  defineProps(['pokemons'])
  </script>