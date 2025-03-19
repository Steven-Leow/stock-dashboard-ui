<template>
  <div class="col-3 bg-light border-end p-3 d-flex flex-column">
    <!-- Home Button -->
    <button class="btn btn-secondary mb-3" @click="$emit('reset')">Home</button>

    <!-- Search Bar -->
    <input
      v-model="searchQuery"
      type="text"
      class="form-control mb-2"
      placeholder="Search stocks..."
      @keyup.enter="submitSearch"
    />
    <button class="btn btn-primary mb-3" @click="submitSearch">Search</button>
    <h5 class="text-muted">Menu</h5>
    <ul class="list-group flex-grow-1 overflow-auto">
      <li class="list-group-item">Watchlist</li>
      <li class="list-group-item">Portfolio</li>
      <li class="list-group-item">News</li>
      <li class="list-group-item">Settings</li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from "vue";

const emit = defineEmits(["search"]);
const searchQuery = ref("");

const submitSearch = () => {
  emit("search", searchQuery.value.toUpperCase());
};
</script>
