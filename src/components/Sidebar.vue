<template>
  <div class="col-3 bg-light border-end p-3 d-flex flex-column">
    <input
      v-model="searchQuery"
      type="text"
      class="form-control mb-2"
      placeholder="Search stocks..."
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

<script>
export default {
  data() {
    return { searchQuery: "" };
  },
  methods: {
    submitSearch() {
      this.$emit("search", this.searchQuery.toUpperCase());
    },
  },
};
</script>
