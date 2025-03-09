<template>
  <div class="col-3 bg-light border-start p-3 d-flex flex-column">
    <h5 class="text-muted">{{ stock ? stock.name : "Latest News" }}</h5>
    <ul v-if="!stock" class="list-group flex-grow-1 overflow-auto">
      <li class="list-group-item">Stock Market Rises Today</li>
      <li class="list-group-item">Tech Stocks Surge</li>
      <li class="list-group-item">Investors Eye Fed Policy</li>
    </ul>
    <div v-if="stock" class="stock-info">
      <p><strong>Price:</strong> {{ stock.price }}</p>
      <p><strong>Change:</strong> {{ stock.change }}</p>
      <p><strong>Volume:</strong> {{ stock.volume }}</p>
    </div>
  </div>
</template>

<script>
export default {
  props: ["stock"],
};
</script>
