<script setup>
import { ref, onMounted } from "vue";
import { useRoute } from "vue-router";

const route = useRoute(); // Accéder aux informations de la route
const pokemonId = route.params.id; // Récupérer l'ID depuis l'URL
const pokemonDetails = ref(null); // Détails d'un seul Pokémon
const isLoading = ref(true);
const error = ref(null);

async function fetchPokemonDetails() {
  try {
    const response = await fetch(
      `https://pokeapi.co/api/v2/pokemon/${pokemonId}`
    );
    if (!response.ok) {
      throw new Error("Impossible de charger les détails du Pokémon.");
    }
    const data = await response.json();

    // Stocker les détails du Pokémon
    pokemonDetails.value = {
      id: data.id,
      name: data.name,
      image: data.sprites.front_default,
      types: data.types.map((type) => type.type.name), // Récupère les types du Pokémon
      stats: data.stats.map((stat) => ({
        name: stat.stat.name,
        value: stat.base_stat,
      })), // Récupère les statistiques
    };
  } catch (err) {
    error.value = err;
  } finally {
    isLoading.value = false;
  }
}

onMounted(() => {
  fetchPokemonDetails();
});
</script>

<template>
  <div class="p-4">
    <div v-if="isLoading" class="text-center text-gray-500 text-lg">
      Chargement en cours...
    </div>
    <div v-else-if="error" class="text-center text-red-500 text-lg">
      Une erreur est survenue : {{ error.message }}
    </div>
    <div v-else class="max-w-lg mx-auto bg-white shadow-md rounded-lg p-6">
      <h1 class="text-2xl font-bold text-center capitalize mb-4">
        {{ pokemonDetails.name }}
      </h1>
      <img
        :src="pokemonDetails.image"
        :alt="pokemonDetails.name"
        class="w-32 h-32 mx-auto mb-4"
      />
      <h2 class="text-lg font-semibold">Types :</h2>
      <ul class="flex space-x-2 mb-4">
        <li
          v-for="type in pokemonDetails.types"
          :key="type"
          class="px-2 py-1 bg-blue-200 text-blue-800 rounded"
        >
          {{ type }}
        </li>
      </ul>
      <h2 class="text-lg font-semibold">Statistiques :</h2>
      <ul>
        <li
          v-for="stat in pokemonDetails.stats"
          :key="stat.name"
          class="flex justify-between py-1"
        >
          <span class="capitalize">{{ stat.name }}</span>
          <span>{{ stat.value }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>
