<template>
  <div v-if="!loaded">
    <p class="text-xl">
      <center>
        <svg
          class="animate-spin h-4 w-4 rounded-full bg-transparent border-2 border-transparent border-opacity-90"
          style="border-right-color: white; border-top-color: white"
          viewBox="0 0 24 24"
        ></svg>
        Upload CSV
      </center>
    </p>
  </div>
  <div v-else class="flex flex-col">
    <div>
      <LineChart
        v-bind:chartDataset="chartPortfolioData"
        v-bind:chartLabels="chartLabels"
      />
    </div>
    <div>
      <PieChart
        v-bind:chartDataset="chartPercentiles"
        v-bind:chartLabels="chartLabels"
      />
    </div>
  </div>
</template>

<script>
import PieChart from "./PieChart.vue";
import LineChart from "./LineChart.vue";

export default {
  data: function () {
    return {
      chartLabels: [],
      chartPercentiles: [],
      loaded: false,
    };
  },
  props: ["chartdata"],
  components: {
    PieChart,
    LineChart,
  },
  methods: {
    generateChartLabels() {
      const handler = {
        get(target, property) {
          return target[property];
        },
      };
      const proxy = new Proxy(this.chartdata, handler);

      //console.log("generateChartLabels", this.chartdata);
      var labels = [];
      var percents = [];
      for (const i in proxy) {
        labels.push(proxy[i]["Symbol"]);
        percents.push(parseFloat(proxy[i]["Portfolio Percent"]));
      }
      this.chartLabels = labels;
      this.chartPercentiles = percents;
      //console.log("loaded set to true");
      this.loaded = true;
    },
  },
  watch: {
    chartdata(newChartData) {
      const handler = {
        get(target, property) {
          return target[property];
        },
      };
      const proxy = new Proxy(newChartData, handler);
      if (proxy.length !== 0) {
        console.log("Chartdata has data and is updated");
        this.generateChartLabels();
      }
    },
  },
};
</script>
