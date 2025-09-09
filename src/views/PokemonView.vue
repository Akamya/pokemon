<script setup>
import { ref, onMounted } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const pokemonId = route.params.id || "1"; // Provide fallback ID

const pokemonDetails = ref(null);
const isLoading = ref(true);
const error = ref(null);

const typeColors = {
  normal: "bg-gray-400",
  fire: "bg-red-500",
  water: "bg-blue-500",
  electric: "bg-yellow-400",
  grass: "bg-green-500",
  ice: "bg-blue-300",
  fighting: "bg-red-700",
  poison: "bg-purple-500",
  ground: "bg-yellow-600",
  flying: "bg-indigo-400",
  psychic: "bg-pink-500",
  bug: "bg-green-400",
  rock: "bg-yellow-800",
  ghost: "bg-purple-700",
  dragon: "bg-indigo-700",
  dark: "bg-gray-800",
  steel: "bg-gray-500",
  fairy: "bg-pink-300",
};

function getStatColor(value) {
  if (value >= 100) return "bg-green-500";
  if (value >= 80) return "bg-yellow-500";
  if (value >= 60) return "bg-orange-500";
  return "bg-red-500";
}

async function fetchPokemonDetails() {
  try {
    const response = await fetch(
      `https://pokeapi.co/api/v2/pokemon/${pokemonId}`
    );
    if (!response.ok) {
      throw new Error("Impossible de charger les détails du Pokémon.");
    }
    const data = await response.json();

    pokemonDetails.value = {
      id: data.id,
      name: data.name,
      image: data.sprites.front_default,
      types: data.types.map((type) => type.type.name),
      stats: data.stats.map((stat) => ({
        name: stat.stat.name,
        value: stat.base_stat,
      })),
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
  <div
    class="min-h-screen bg-gradient-to-br from-emerald-50 via-white to-teal-50"
  >
    <div
      v-if="isLoading"
      class="flex flex-col items-center justify-center min-h-screen"
    >
      <div
        class="animate-spin rounded-full h-16 w-16 border-4 border-emerald-500 border-t-transparent mb-4"
      ></div>
      <p class="text-xl text-gray-600 font-['DM_Sans']">
        Loading Pokémon details...
      </p>
    </div>

    <div
      v-else-if="error"
      class="flex items-center justify-center min-h-screen"
    >
      <div
        class="bg-red-50 border border-red-200 rounded-xl p-8 max-w-md mx-auto"
      >
        <p class="text-red-600 text-lg font-['DM_Sans'] text-center">
          {{ error.message }}
        </p>
      </div>
    </div>

    <div v-else class="container mx-auto px-4 py-8">
      <router-link
        to="/"
        class="inline-flex items-center text-emerald-600 hover:text-emerald-700 mb-8 font-['DM_Sans'] font-medium transition-colors duration-200"
      >
        <svg
          class="w-5 h-5 mr-2"
          fill="none"
          stroke="currentColor"
          viewBox="0 0 24 24"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M15 19l-7-7 7-7"
          />
        </svg>
        Back to Pokédex
      </router-link>

      <div class="max-w-4xl mx-auto">
        <div class="bg-white rounded-3xl shadow-2xl overflow-hidden mb-8">
          <div
            class="bg-gradient-to-br from-emerald-500 to-teal-600 px-8 py-12 text-center text-white"
          >
            <h1
              class="text-5xl font-bold capitalize mb-2 font-['Space_Grotesk']"
            >
              {{ pokemonDetails.name }}
            </h1>
            <p class="text-emerald-100 text-xl font-['DM_Sans']">
              #{{ pokemonDetails.id.toString().padStart(3, "0") }}
            </p>
          </div>

          <div class="px-8 py-12">
            <div class="flex flex-col lg:flex-row items-center gap-12">
              <div class="flex-shrink-0">
                <div
                  class="bg-gradient-to-br from-gray-50 to-gray-100 rounded-3xl p-8 shadow-inner"
                >
                  <img
                    :src="pokemonDetails.image"
                    :alt="pokemonDetails.name"
                    class="w-48 h-48 object-contain mx-auto"
                  />
                </div>
              </div>

              <div class="flex-1 text-center lg:text-left">
                <h2
                  class="text-2xl font-bold mb-6 font-['Space_Grotesk'] text-gray-800"
                >
                  Types
                </h2>
                <div
                  class="flex flex-wrap justify-center lg:justify-start gap-3 mb-8"
                >
                  <span
                    v-for="type in pokemonDetails.types"
                    :key="type"
                    :class="typeColors[type] || 'bg-gray-400'"
                    class="px-6 py-3 text-white rounded-full font-bold capitalize text-lg font-['DM_Sans'] shadow-lg"
                  >
                    {{ type }}
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="bg-white rounded-3xl shadow-2xl p-8">
          <h2
            class="text-3xl font-bold mb-8 font-['Space_Grotesk'] text-gray-800 text-center"
          >
            Base Stats
          </h2>
          <div class="space-y-6">
            <div
              v-for="stat in pokemonDetails.stats"
              :key="stat.name"
              class="flex items-center gap-6"
            >
              <div class="w-32 text-right">
                <span
                  class="font-semibold capitalize text-gray-700 font-['DM_Sans']"
                >
                  {{ stat.name.replace("-", " ") }}
                </span>
              </div>

              <div
                class="flex-1 bg-gray-200 rounded-full h-4 relative overflow-hidden"
              >
                <div
                  :class="getStatColor(stat.value)"
                  class="h-full rounded-full transition-all duration-1000 ease-out relative"
                  :style="{
                    width: `${Math.min((stat.value / 150) * 100, 100)}%`,
                  }"
                >
                  <div class="absolute inset-0 bg-white/20 animate-pulse"></div>
                </div>
              </div>

              <div class="w-16 text-left">
                <span class="font-bold text-lg text-gray-800 font-['DM_Sans']">
                  {{ stat.value }}
                </span>
              </div>
            </div>
          </div>

          <div class="mt-8 pt-6 border-t border-gray-200">
            <div class="flex justify-center">
              <div class="bg-emerald-50 rounded-2xl px-8 py-4">
                <span class="text-emerald-700 font-semibold font-['DM_Sans']"
                  >Total:
                </span>
                <span
                  class="text-2xl font-bold text-emerald-800 font-['Space_Grotesk']"
                >
                  {{
                    pokemonDetails.stats.reduce(
                      (sum, stat) => sum + stat.value,
                      0
                    )
                  }}
                </span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
