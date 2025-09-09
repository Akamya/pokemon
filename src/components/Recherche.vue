<script setup>
import { ref } from "vue";

// Liste des suggestions initiales (peut être chargée dynamiquement aussi)
const props = defineProps({
  items: {
    type: Array,
    required: true,
    default: [],
  },
});

// Variables réactives pour la recherche et les suggestions filtrées
const search = ref("");
const filteredSuggestions = ref([]);
const emit = defineEmits(["filtered-items"]);

// Fonction appelée lorsque l'utilisateur tape dans le champ de recherche
function onInput() {
  if (search.value) {
    // Filtrer les suggestions en fonction de la recherche
    filteredSuggestions.value = props.items.filter((suggestion) =>
      suggestion.name.toLowerCase().includes(search.value.toLowerCase())
    );
  } else {
    // Si rien n'est tapé, pas de suggestions
    filteredSuggestions.value = props.items;
  }
  emit("filtered-items", filteredSuggestions.value); // Émettre l'événement
}
</script>

<template>
  <!-- Completely redesigned search input with modern styling -->
  <div class="relative">
    <div
      class="absolute inset-y-0 left-0 pl-4 flex items-center pointer-events-none"
    >
      <!-- Added search icon -->
      <svg
        class="h-5 w-5 text-gray-400"
        fill="none"
        stroke="currentColor"
        viewBox="0 0 24 24"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
        />
      </svg>
    </div>

    <input
      type="text"
      v-model="search"
      placeholder="Search Pokémon by name..."
      @input="onInput"
      class="w-full pl-12 pr-4 py-4 text-lg bg-white border-2 border-gray-200 rounded-2xl shadow-lg focus:outline-none focus:ring-4 focus:ring-emerald-500/20 focus:border-emerald-500 transition-all duration-300 font-['DM_Sans'] placeholder-gray-400"
    />

    <!-- Added clear button when there's text -->
    <div
      v-if="search"
      class="absolute inset-y-0 right-0 pr-4 flex items-center"
    >
      <button
        @click="
          search = '';
          onInput();
        "
        class="text-gray-400 hover:text-gray-600 transition-colors duration-200"
      >
        <svg
          class="h-5 w-5"
          fill="none"
          stroke="currentColor"
          viewBox="0 0 24 24"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M6 18L18 6M6 6l12 12"
          />
        </svg>
      </button>
    </div>
  </div>
</template>
