<script setup>
import { Pie } from 'vue-chartjs';
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  ArcElement,
} from 'chart.js';

// Register Chart.js components
ChartJS.register(Title, Tooltip, Legend, ArcElement);

const props = defineProps({
  data: {
    type: Object,
    required: true,
  },
  options: {
    type: Object,
    default: () => ({
      responsive: true, // Ensures the chart resizes with the container
      maintainAspectRatio: false, // Allows the chart to adapt to the container's aspect ratio
    }),
  },
});
</script>

<template>
  <div style="position: relative; width: 100%; height: 100%;">
    <Pie :data="data" :options="options" />
  </div>
</template>