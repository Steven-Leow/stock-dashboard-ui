<template>
  <div class="col-6 p-3">
    <!-- Display welcome message when no stock data is available -->
    <h3 v-if="!stockData">Welcome to Your Stock Dashboard</h3>
    <p v-if="!stockData" class="text-muted">
      Easily track your favorite stocks, manage your portfolio, and stay updated
      with the latest market news.
    </p>

    <!-- Stock chart canvas -->
    <canvas v-if="stockData" ref="stockChart"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, nextTick } from "vue";
import Chart from "chart.js/auto";
import zoomPlugin from "chartjs-plugin-zoom";

// Register zoom plugin for Chart.js
Chart.register(zoomPlugin);

// Define component props
const props = defineProps({
  stockData: Object,
  selectedStock: String, // Accept string instead of object
});

// Reference to the canvas element
const stockChart = ref(null);
let chartInstance = null;

/**
 * Parses stock data into labels (dates) and closing prices.
 * @returns {Object} An object containing labels (dates) and data (closing prices).
 */
const parseStockData = () => {
  if (!props.stockData) return { labels: [], data: [] };
  const labels = Object.keys(props.stockData);
  const data = labels.map((date) =>
    parseFloat(props.stockData[date]["4. close"])
  );

  return { labels, data, ticker: props.selectedStock || "Unknown" };
};
/**
 * Creates and renders the stock chart.
 */
const createChart = async () => {
  await nextTick(); // Ensure the DOM updates before rendering

  if (!stockChart.value) {
    console.error("Canvas element not found!");
    return;
  }

  const { labels, data, ticker } = parseStockData();

  // Destroy existing chart instance before creating a new one
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
          borderWidth: 2,
          tension: 0.2,
          pointRadius: 3,
          pointHoverRadius: 10,
          pointBackgroundColor: "#42A5F5",
          fill: true,
          backgroundColor: (context) => {
            const gradient = context.chart.ctx.createLinearGradient(
              0,
              0,
              0,
              400
            );
            gradient.addColorStop(0, "rgba(66, 165, 245, 1.0)");
            gradient.addColorStop(1, "rgba(66, 165, 245, 0.0)");
            return gradient;
          },
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        x: { title: { display: true, text: "Date" } },
        y: {
          title: { display: true, text: "Price (USD)" },
          afterDataLimits: (scale) => {
            scale.max = scale.max * 1.1;
          },
        },
      },
      plugins: {
        title: {
          display: true,
          text: `${ticker} Price Over Time`,
          font: {
            size: 18,
            weight: "bold",
          },
          padding: {
            top: 10,
            bottom: 20,
          },
        },
        tooltip: {
          enabled: true,
          callbacks: {
            label: (tooltipItem) => `Price: $${tooltipItem.raw.toFixed(2)}`,
          },
        },
        zoom: {
          pan: {
            enabled: true,
            mode: "xy",
          },
          zoom: {
            wheel: {
              enabled: true,
              speed: 0.05,
            },
            pinch: {
              enabled: true,
            },
            mode: "xy",
          },
          limits: {
            y: { min: "original", max: "original" },
            x: { min: "original", max: "original" },
          },
        },
        legend: {
          onClick: function () {
            chartInstance.resetZoom();
          },
          labels: {
            generateLabels: function (chart) {
              const originalLabels =
                Chart.defaults.plugins.legend.labels.generateLabels(chart);
              originalLabels.push({
                text: "Reset Zoom",
                fillStyle: "#f87979",
                strokeStyle: "#f87979",
                lineWidth: 0,
              });
              return originalLabels;
            },
          },
        },
        animation: {
          duration: 1200,
          easing: "easeInOutQuad",
        },
      },
    },
  });
};

// Initialize chart on mount
onMounted(createChart);

// Watch for stock data changes and update chart
watch(() => props.stockData, createChart, { deep: true });
</script>
