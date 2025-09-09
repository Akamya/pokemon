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
  <div
    class="min-h-screen bg-gradient-to-br from-emerald-50 via-white to-teal-50"
  >
    <!-- Added hero section with improved typography -->
    <div class="bg-gradient-to-r from-emerald-600 to-teal-600 text-white py-16">
      <div class="container mx-auto px-4 text-center">
        <h1 class="text-5xl font-bold mb-4 font-['Space_Grotesk']">
          Pokédex Explorer
        </h1>
        <p class="text-xl opacity-90 font-['DM_Sans']">
          Discover and explore the world of Pokémon
        </p>
      </div>
    </div>

    <!-- Improved search section with better spacing -->
    <div class="container mx-auto px-4 py-8">
      <div class="max-w-2xl mx-auto mb-12">
        <Recherche :items="pokemonList" @filtered-items="handleFilter" />
      </div>

      <!-- Enhanced loading and error states -->
      <div
        v-if="isLoading"
        class="flex flex-col items-center justify-center py-20"
      >
        <div
          class="animate-spin rounded-full h-16 w-16 border-4 border-emerald-500 border-t-transparent mb-4"
        ></div>
        <p class="text-xl text-gray-600 font-['DM_Sans']">Loading Pokémon...</p>
      </div>

      <div v-else-if="error" class="text-center py-20">
        <div
          class="bg-red-50 border border-red-200 rounded-xl p-8 max-w-md mx-auto"
        >
          <p class="text-red-600 text-lg font-['DM_Sans']">
            {{ error.message }}
          </p>
        </div>
      </div>

      <!-- Completely redesigned Pokemon grid with hover effects -->
      <div
        v-else
        class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-6"
      >
        <router-link
          v-for="pokemon in selectedPokemons"
          :key="pokemon.id"
          :to="`/pokemon/${pokemon.id}`"
          class="group bg-white rounded-2xl shadow-lg hover:shadow-2xl transition-all duration-300 transform hover:-translate-y-2 overflow-hidden border border-gray-100"
        >
          <!-- Added gradient header for each card -->
          <div
            class="bg-gradient-to-br from-emerald-400 to-teal-500 p-4 text-center"
          >
            <h3
              class="text-lg font-bold text-white capitalize font-['Space_Grotesk'] mb-2"
            >
              {{ pokemon.name }}
            </h3>
            <span class="text-emerald-100 text-sm font-['DM_Sans']"
              >#{{ pokemon.id.toString().padStart(3, "0") }}</span
            >
          </div>

          <!-- Improved image section with better sizing -->
          <div class="p-6 flex flex-col items-center">
            <div
              class="bg-gray-50 rounded-full p-4 mb-4 group-hover:bg-emerald-50 transition-colors duration-300"
            >
              <img
                :src="pokemon.image"
                :alt="pokemon.name"
                class="w-20 h-20 object-contain group-hover:scale-110 transition-transform duration-300"
              />
            </div>

            <!-- Added explore button -->
            <div class="text-center">
              <span
                class="inline-flex items-center px-4 py-2 bg-emerald-100 text-emerald-700 rounded-full text-sm font-medium font-['DM_Sans'] group-hover:bg-emerald-200 transition-colors duration-300"
              >
                Explore →
              </span>
            </div>
          </div>
        </router-link>
      </div>

      <!-- Added results counter -->
      <div v-if="!isLoading && !error" class="text-center mt-12">
        <p class="text-gray-600 font-['DM_Sans']">
          Showing {{ selectedPokemons.length }} of
          {{ pokemonList.length }} Pokémon
        </p>
      </div>
    </div>
  </div>
</template>
