<template>
  <Doughnut id="progressbar" :options="chartOptions" :data="pieData" />
</template>

<script>
import { Chart as ChartJS, ArcElement } from "chart.js";

import { Doughnut } from "vue-chartjs";

ChartJS.register(ArcElement);

export default {
  name: "Doughnuts",
  components: { Doughnut },
  props: ["duration", "timerTicker", "pieData"],
  data() {
    return {
      chartData: {
        datasets: [
          {
            data: [50, 50],
            backgroundColor: ["red", "blue"],
            cutout: "95%",
          },
        ],
      },
      chartOptions: {
        animation: false,
        responsive: false,
        borderWidth: 0,
        plugins: {
          tooltip: {
            enabled: false,
          },
          legend: {
            display: false,
          },
        },
      },
    };
  },
};
</script>
