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
    <div class="mt-8">
      <!--
      <button
        class="text-xs border border-transparent rounded-md text-white hover:text-gray-300 bg-indigo-600 hover:bg-indigo-700 focus:outline-none"
        @click="captureClick"
      >
        {{ options[selectedOption].label }}
        <i class="ml-1 mdi mdi-chevron-down"></i>
      </button>
      -->
      <div class="relative inline-block text-right dropdown">
        <span class="rounded-md"
          ><button
            class="inline-flex justify-center w-full px-4 py-2 text-sm font-medium leading-5 text-gray-700 transition duration-150 ease-in-out border border-gray-300 rounded-md hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue active:bg-gray-50 active:text-gray-800"
            type="button"
            aria-haspopup="true"
            aria-expanded="true"
            aria-controls="headlessui-menu-items-117"
          >
            <span>Today</span>
            <svg
              class="w-5 h-5 ml-2 -mr-1"
              viewBox="0 0 20 20"
              fill="currentColor"
            >
              <path
                fill-rule="evenodd"
                d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
                clip-rule="evenodd"
              ></path>
            </svg></button
        ></span>
        <div
          class="opacity-0 invisible dropdown-menu transition-all duration-300 transform origin-top-right -translate-y-2 scale-95"
        >
          <div
            class="absolute right-0 w-56 mt-2 origin-top-right border border-gray-200 divide-y divide-gray-100 rounded-md shadow-lg outline-none"
            aria-labelledby="headlessui-menu-button-1"
            id="headlessui-menu-items-117"
            role="menu"
          >
            <div class="py-1">
              <a
                href=""
                tabindex="0"
                class="text-sm flex justify-between text-right leading-5 w-full"
                role="menuitem"
                >5 days</a
              >
              <a
                href=""
                tabindex="1"
                class="text-sm flex justify-between text-right leading-5 w-full"
                role="menuitem"
                >30 days</a
              >
            </div>
          </div>
        </div>
      </div>

      <LineChart
        v-bind:chartDataset="chartPortfolioData"
        v-bind:chartLabels="chartLabels"
      />
    </div>
    <div class="mt-8">
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
      date: "today",
      options: [
        {
          label: "Today",
          value: "today",
        },
        {
          label: "Last 7 Days",
          value: "7days",
        },
        {
          label: "Last 30 Days",
          value: "30days",
        },
        {
          label: "Last 6 Months",
          value: "6months",
        },
        {
          label: "This Year",
          value: "year",
        },
      ],
      showDropdown: false,
      selectedOption: 0,
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
    captureClick() {
      console.log(this.options[this.selectedOption].label);
      console.log("captured click");
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

<style>
.dropdown:focus-within .dropdown-menu {
  opacity: 1;
  transform: translate(0) scale(1);
  visibility: visible;
}
</style>