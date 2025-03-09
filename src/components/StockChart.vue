<template>
  <div class="col-6 p-3">
    <h3 v-if="!stockData">Welcome to Your Stock Dashboard</h3>
    <p v-if="!stockData" class="text-muted">
      Easily track your favorite stocks, manage your portfolio, and stay updated
      with the latest market news.
    </p>

    <canvas v-if="stockData" ref="stockChart"></canvas>
  </div>
</template>

<script>
import { ref, onMounted, watch, nextTick } from "vue";
import Chart from "chart.js/auto";

export default {
  props: {
    stockData: Object,
  },
  setup(props) {
    const stockChart = ref(null);
    let chartInstance = null;

    const parseStockData = () => {
      if (!props.stockData) return { labels: [], data: [] };
      const labels = Object.keys(props.stockData);
      const data = labels.map((date) =>
        parseFloat(props.stockData[date]["4. close"])
      );
      return { labels, data };
    };

    const createChart = async () => {
      await nextTick(); // Ensure the DOM updates first

      if (!stockChart.value) {
        console.error("Canvas element not found!");
        return;
      }

      const { labels, data } = parseStockData();

      if (chartInstance) {
        chartInstance.destroy();
      }

      chartInstance = new Chart(stockChart.value, {
        type: "line",
        data: {
          labels,
          datasets: [
            {
              label: "Stock Price (Close)",
              data,
              borderColor: "#42A5F5",
              backgroundColor: "rgba(66, 165, 245, 0.2)",
              borderWidth: 2,
              tension: 0.3,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            x: { title: { display: true, text: "Date" } },
            y: { title: { display: true, text: "Price (USD)" } },
          },
        },
      });
    };

    onMounted(createChart);
    watch(() => props.stockData, createChart, { deep: true });

    return { stockChart };
  },
};
</script>
