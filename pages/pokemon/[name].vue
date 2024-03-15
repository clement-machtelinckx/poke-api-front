<template>
    <div>
      <div v-if="pokemon" class="flex flex-wrap justify-center">
        <div class="w-full md:w-1/2 lg:w-1/3 p-4">
          <h1 class="text-2xl font-bold mb-2">{{ pokemon.name }}</h1>
          <img :src="pokemon.sprites.front_default" alt="pokemon" class="mb-4" />
          <img :src="pokemon.sprites.front_shiny" alt="pokemon" class="mb-4" />
          <img :src="pokemon.sprites.back_default" alt="pokemon" class="mb-4" />
          <img :src="pokemon.sprites.back_shiny" alt="pokemon" class="mb-4" />
          <p class="font-bold">Height: {{ pokemon.height }}</p>
          <p class="font-bold">Weight: {{ pokemon.weight }}</p>
          <p class="font-bold">Type: {{ pokemon.types[0].type.name }}</p>
          <p class="font-bold">Game ID: {{ pokemon.game_indices[3].game_index }}</p>

        </div>
  
        <div class="w-full md:w-1/2 lg:w-1/3 p-4">
          <h2 class="text-2xl font-bold mb-2">Stats</h2>
          <ul class="list-disc list-inside">
            <li v-for="stat in pokemon.stats" :key="stat.stat.name">{{ stat.stat.name }}: {{ stat.base_stat }}</li>
          </ul>
          <div>
            <p>hp: {{ pokemon.stats[0].base_stat }}</p>
            <p>attack: {{ pokemon.stats[1].base_stat }}</p>
            <p>defence: {{ pokemon.stats[2].base_stat }}</p>
            <p>special-attack: {{ pokemon.stats[3].base_stat }}</p>
            <p>special-deffence: {{ pokemon.stats[4].base_stat }}</p>
            <p>speed: {{ pokemon.stats[5].base_stat }}</p>
          </div>
        </div>
      </div>
      <div v-else class="text-center">
        <p>Article not found</p>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
import { ref, computed } from 'vue';
import { useRoute } from 'vue-router';
import { useFetch } from '#app';

const route = useRoute();
const { data: pokemon, execute: fetchPokemon } = useFetch(`https://pokeapi.co/api/v2/pokemon/${route.params.name}`, {
  initialCache: false,
});

await fetchPokemon();

const type1 = computed(() => pokemon.value?.types[0]?.type?.name || '');
const type2 = computed(() => pokemon.value?.types[1]?.type?.name || null);
const imgFront = computed(() => pokemon.value?.sprites?.front_default || '');
const imgFrontShiny = computed(() => pokemon.value?.sprites?.front_shiny || '');

const pokemonData = computed(() => ({
  gameId: pokemon.value?.game_indices[3]?.game_index || null,
  name: pokemon.value?.name || '',
  height: pokemon.value?.height || null,
  weight: pokemon.value?.weight || null,
  hp: pokemon.value?.stats[0]?.base_stat || null,
  attack: pokemon.value?.stats[1]?.base_stat || null,
  defense: pokemon.value?.stats[2]?.base_stat || null,
  specialAttack: pokemon.value?.stats[3]?.base_stat || null,
  specialDefense: pokemon.value?.stats[4]?.base_stat || null,
  speed: pokemon.value?.stats[5]?.base_stat || null,
  type1: type1.value,
  type2: type2.value,
  description: "",
  createdAt: new Date().toISOString(),
  updatedAt: new Date().toISOString(),
}));

const PDJSON = computed(() => JSON.stringify(pokemonData.value));

console.log(PDJSON);
console.log(pokemonData);

const createPokemon = async () => {
  if (pokemonData.value) {
    const response = await fetch('https://localhost:8000/api/pokema', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/ld+json', // Utiliser application/ld+json au lieu de application/json
      },
      body: JSON.stringify(pokemonData.value, null, 2),
    });

    if (!response.ok) {
      const error = await response.json();
      console.error('Error creating pokemon:', error);
    } else {
      console.log('Pokemon created successfully');
    }
  }
};

createPokemon();
  
  </script>
  