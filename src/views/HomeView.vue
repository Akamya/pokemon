<script setup>
import { ref, onMounted } from "vue";
import Recherche from "../components/Recherche.vue";

const pokemonList = ref([]);
const isLoading = ref(true);
const error = ref(null);
const selectedPokemons = ref([]);

function handleFilter(filteredPokemons) {
  selectedPokemons.value = filteredPokemons;
}

async function fetchPokemonList() {
  try {
    const response = await fetch("https://pokeapi.co/api/v2/pokemon?limit=151");
    const data = await response.json();
    // console.log(data.results);

    // Récupérer les détails pour chaque Pokémon
    const detailedPokemonList = await Promise.all(
      data.results.map((pokemon, index) => {
        const id = index + 1;
        // const detailsResponse = await fetch(pokemon.url);
        // const detailsData = await detailsResponse.json();
        return {
          id: id,
          name: pokemon.name,
          image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${id}.png`,
        };
      })
    );

    pokemonList.value = detailedPokemonList;
    selectedPokemons.value = detailedPokemonList;
  } catch (err) {
    error.value = err;
  } finally {
    isLoading.value = false;
  }
}

onMounted(() => {
  fetchPokemonList();
});
</script>

<template>
  <div id="app">
    <Recherche :items="pokemonList" @filtered-items="handleFilter"></Recherche>
  </div>

  <div class="p-4">
    <div v-if="isLoading" class="text-center text-gray-500 text-lg">
      Chargement en cours...
    </div>
    <div v-else-if="error" class="text-center text-red-500 text-lg">
      Une erreur est survenue : {{ error.message }}
    </div>
    <div
      v-else
      class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 gap-4"
    >
      <router-link
        v-for="pokemon in selectedPokemons"
        :key="pokemon.id"
        :to="`/pokemon/${pokemon.id}`"
        class="card bg-white shadow-md rounded-lg p-4 flex flex-col items-center text-center"
      >
        <h3 class="text-lg font-bold capitalize mb-2">{{ pokemon.name }}</h3>
        <img
          :src="pokemon.image"
          :alt="pokemon.name"
          class="w-24 h-24 object-contain"
        />
      </router-link>
    </div>
  </div>
</template>
