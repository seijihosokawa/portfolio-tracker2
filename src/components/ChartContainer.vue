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

      <LineChart
        v-bind:chartDataset="lineChartData"
        v-bind:chartLabels="lineChartLabels"
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
#sortbox:checked ~ #sortboxmenu {
  opacity: 1;
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