<template>
  <div id="app" class="container-fluid vh-100 d-flex">
    <Sidebar @search="fetchStockData" />
    <StockChart :stockData="stockData" />
    <StockInfo :stockInfo="selectedStock" />
  </div>
</template>

<script>
import Sidebar from "./components/Sidebar.vue";
import StockChart from "./components/StockChart.vue";
import StockInfo from "./components/StockInfo.vue";
import stockDataJson from "./assets/stock_data.json";
import { ref } from "vue";

export default {
  components: { Sidebar, StockChart, StockInfo },
  setup() {
    const stockData = ref(null);
    const selectedStock = ref(null);

    const fetchStockData = (searchQuery) => {
      console.log("Fetching stock data...");
      stockData.value = stockDataJson["Time Series (Daily)"];
      selectedStock.value = stockDataJson["Meta Data"]["2. Symbol"];
      console.log("Updated stockData:", stockData.value);
    };

    return { stockData, selectedStock, fetchStockData };
  },
};
</script>

<style>
#app > div {
  height: 100vh;
  overflow-y: auto;
}
</style>
