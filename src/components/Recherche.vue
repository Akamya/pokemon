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
  <input
    type="text"
    v-model="search"
    placeholder="Recherche..."
    @input="onInput"
    class="w-full p-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-400 focus:border-blue-400"
  />
</template>
