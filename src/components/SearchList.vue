<template>
  <ion-searchbar :debounce="1000" @ionInput="handleInput($event)" placeholder="Search weather date"></ion-searchbar>
</template>

<script setup lang="ts">
import { IonSearchbar } from "@ionic/vue";

// Declare event: 'searchQuery'
const emit = defineEmits<{(e: "searchQuery", query: string): void;}>();

// Handle input event
const handleInput = (i: any) => {
  // Get input value and convert to lowercase
  const query: string = i.target.value.toLowerCase();

  // Send input value to parent component
  emit("searchQuery", query);
};
</script>