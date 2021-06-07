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
      <div class="relative float-right">
        <input type="checkbox" id="sortbox" class="hidden absolute" />
        <label for="sortbox" class="flex items-center space-x-1 cursor-pointer">
          <span class="text-base">{{ options[selectedOption].label }}</span>
          <svg
            class="h-4 w-4"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M19 9l-7 7-7-7"
            />
          </svg>
        </label>

        <div
          id="sortboxmenu"
          class="absolute right-1 top-full min-w-max rounded-2xl opacity-0 border-2 border-gray-400 transition delay-75 ease-in-out z-10"
        >
          <ul class="block mb-1 text-right text-gray-300">
            <li v-for="(option, index) in options" :key="option.id">
              <a
                v-if="option.label != options[selectedOption].label"
                @click="optionClicked(index)"
                class="block px-2 py-2 cursor-pointer"
                >{{ option.label }}</a
              >
            </li>
          </ul>
        </div>
      </div>
      -->
      <div class="relative inline-block text-right dropdown float-right">
        <span class="rounded-md shadow-sm"
          ><button
            class="inline-flex justify-right w-full px-4 py-2 text-sm font-medium leading-5 text-gray-700 transition duration-150 ease-in-out border border-gray-300 rounded-md hover:text-gray-500"
            type="button"
            aria-haspopup="true"
            aria-expanded="true"
            aria-controls="headlessui-menu-items-117"
          >
            <span>Options</span>
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
            class="absolute right-0 w-56 mt-2 origin-top-right border border-gray-200 rounded-md"
            aria-labelledby="headlessui-menu-button-1"
            id="headlessui-menu-items-117"
            role="menu"
          >
            <div
              class="py-1 text-right"
              v-for="(option, index) in options"
              :key="option.id"
            >
              <a
                v-if="option.label != options[selectedOption].label"
                @click="optionClicked(index)"
                class="block px-2 py-2 cursor-pointer"
                >{{ option.label }}</a
              >
            </div>
          </div>
        </div>
      </div>

      <LineChart
        v-bind:chartDataset="lineChartData"
        v-bind:chartLabels="lineChartLabels"
        v-bind:dateRange="options[selectedOption]"
      />
    </div>
    <div class="mt-8">
      <PieChart
        v-bind:chartDataset="pieChartPercentiles"
        v-bind:chartLabels="pieChartLabels"
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
      pieChartLabels: [],
      pieChartPercentiles: [],
      lineChartLabels: [],
      lineChartData: [],
      loaded: false,
      date: "today",
      options: [
        {
          label: "Today",
          value: "1d",
        },
        {
          label: "Last 5 Days",
          value: "5d",
        },
        {
          label: "Last 30 Days",
          value: "1mo",
        },
        {
          label: "Last 3 Months",
          value: "3mo",
        },
        {
          label: "Last 6 Months",
          value: "6mo",
        },
        {
          label: "YTD",
          value: "ytd",
        },
        {
          label: "1 Year",
          value: "1y",
        },
        {
          label: "5 years",
          value: "5y",
        },
      ],
      selectedOption: 0,
    };
  },
  props: ["chartdata"],
  components: {
    PieChart,
    LineChart,
  },
  methods: {
    generatePieChartLabels() {
      const handler = {
        get(target, property) {
          return target[property];
        },
      };
      const proxy = new Proxy(this.chartdata, handler);

      //console.log("generatePieChartLabels", this.chartdata);
      //creating two arrays to pass to Pie Chart component
      var labels = [];
      var percents = [];
      for (const i in proxy) {
        labels.push(proxy[i]["Symbol"]);
        percents.push(parseFloat(proxy[i]["Portfolio Percent"]));
      }
      this.pieChartLabels = labels;
      this.pieChartPercentiles = percents;
      //console.log("loaded set to true");
      this.loaded = true;
    },
    optionClicked(index) {
      //once a dropdown option is clicked, assign the selected option to chosen index
      console.log(this.options[index]);
      this.selectedOption = index;
    },
  },
  watch: {
    chartdata(newChartData) {
      //need to watch data to grab data if updated
      const handler = {
        get(target, property) {
          return target[property];
        },
      };
      const proxy = new Proxy(newChartData, handler);
      if (proxy.length !== 0) {
        console.log("Chartdata has data and is updated");
        this.generatePieChartLabels();
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

a.block {
  position: relative;
}

a.block:before {
  content: "";
  position: absolute;
  width: 80%;
  margin-left: 10%;
  height: 2px;
  bottom: 0;
  left: 0;
  background-color: #FFF;
  visibility: hidden;
  transform: scaleX(0);
  transition: all 0.3s ease-in-out;
}

a.block:hover:before {
  visibility: visible;
  transform: scaleX(1);
}
</style>