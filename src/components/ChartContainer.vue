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
      <div class="relative inline-block text-right dropdown float-right">
        <span class="rounded-md shadow-sm"
          ><button
            class="inline-flex justify-right w-full px-4 py-2 text-sm font-medium leading-5 text-gray-700 transition duration-150 ease-in-out border border-gray-300 rounded-md hover:text-gray-500"
            type="button"
            aria-haspopup="true"
            aria-expanded="true"
            aria-controls="headlessui-menu-items-117"
          >
            <span>{{ options[previousOption].label }}</span>
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
            class="absolute right-0 w-56 mt-2 origin-top-right border border-gray-200 bg-black rounded-md"
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
        v-bind:chartLabels="lineLabels"
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
//need to move all chart data to here
export default {
  data: function () {
    return {
      pieChartLabels: [],
      pieChartPercentiles: [],
      lineChartData: {},
      lineLabels: [],
      loaded: false,
      dateVal: "1d",
      dateInterval: "15m",
      options: [
        {
          label: "Today",
          value: "1d",
          interval: "15m",
        },
        {
          label: "Last 5 Days",
          value: "5d",
          interval: "1d",
        },
        {
          label: "Last 30 Days",
          value: "1mo",
          interval: "1wk",
        },
        {
          label: "Last 3 Months",
          value: "3mo",
          interval: "1mo",
        },
        {
          label: "Last 6 Months",
          value: "6mo",
          interval: "1mo",
        },
        {
          label: "YTD",
          value: "ytd",
          interval: "1mo",
        },
        {
          label: "1 Year",
          value: "1y",
          interval: "1mo",
        },
        {
          label: "5 years",
          value: "5y",
          interval: "1y",
        },
      ],
      selectedOption: 0,
      previousOption: 0,
    };
  },
  props: ["chartdata"],
  components: {
    PieChart,
    LineChart,
  },
  methods: {
    generatePieChart() {
      const handler = {
        get(target, property) {
          return target[property];
        },
      };
      const proxy = new Proxy(this.chartdata, handler);

      //console.log("generatePieChart", this.chartdata);
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
    },
    async getApiData() {
      var data = await this.makeApiCall();
      //console.log(data);
      return data.indicators.quote[0].close;
    },
    async getApiLabels() {
      var data = await this.makeApiCall();
      //console.log(data);
      var lineChartLabel = data.timestamp;
      lineChartLabel.forEach(function (val, index, arr) {
        let i = 0;
        if (data.meta.range === "1d") {
          i = 1;
        }
        arr[index] = new Date(val * 1000).toLocaleString().split(",")[i];
      });

      return lineChartLabel;
    },
    generateLineChart() {
      console.log("generate chart");
      var dateRange = this.dateVal;
      var interval = this.dateInterval;

      //other token: c4eac4392cmsh76076d1e061f713p1b7aa9jsn6f47c253ffd9
      fetch(
        `https://apidojo-yahoo-finance-v1.p.rapidapi.com/market/get-charts?symbol=%5EGSPC&interval=${interval}&range=${dateRange}&region=US`,
        {
          method: "GET",
          headers: {
            "x-rapidapi-key":
              "b44712c8bfmshfd507b16c8e731bp1c8a77jsned08f4602d3d",
            "x-rapidapi-host": "apidojo-yahoo-finance-v1.p.rapidapi.com",
          },
        }
      )
        .then((response) => response.json())
        .then((data) => {
          console.log(data.chart.result[0]);
          var chartData = data.chart.result[0].indicators.quote[0].close;
          var chartLabels = data.chart.result[0].timestamp;

          chartLabels.forEach(function (val, index, arr) {
            let i = 0;
            if (data.chart.result[0].meta.range === "1d") {
              i = 1;
            }
            arr[index] = new Date(val * 1000).toLocaleString().split(",")[i];
          });

          this.lineChartData = {
            labels: chartLabels,
            datasets: [
              {
                label: "S&P 500",
                data: chartData,
                fill: true,
                borderColor: "#D6ED17FF",
                backgroundColor: "#101820FF",
                tension: 0.1,
              },
            ],
          };
          this.loaded = true;
        })
        .catch((err) => {
          console.error(err);
        });
    },

    optionClicked(index) {
      //once a dropdown option is clicked, assign the selected option to chosen index
      //console.log(this.options[index]);
      this.previousOption = this.selectedOption;
      this.selectedOption = index;
      this.dateVal = this.options[index].value;
      this.dateInterval = this.options[index].interval;
      this.generateLineChart();
    },
  },
  mounted() {
    this.generateLineChart();
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
        this.generatePieChart();
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