<template>
  <nav-bar @update-data="updateData"></nav-bar>
  <!-- First row of info -->
  <div class="flex">
    <!--  Left side row of info boxes -->
    <div class="w-2/3">
      <div class="flex space-x-4 mx-auto px-4">
        <div class="flex-1">
          <portfolio-info-box
            name="Performance Today"
            v-bind:value="todaysPerformance"
            v-bind:percentage="todaysPerformancePercentage"
          ></portfolio-info-box>
        </div>
        <div class="flex-1">
          <portfolio-info-box
            name="Overall Return"
            v-bind:value="overallReturn"
            v-bind:percentage="overallReturnPercentage"
          ></portfolio-info-box>
        </div>
        <div class="flex-1">
          <portfolio-info-box
            name="Portfolio Value"
            v-bind:value="portfolioValue"
          ></portfolio-info-box>
        </div>
      </div>
    </div>
    <!-- End Left side row of info boxes -->

    <!-- Right side row of info boxes -->
    <div class="w-1/3">
      <div class="flex flex-nowrap space-x-4 px-4">
        <div class="flex-auto">
          <market-index-box
            name="S&P 500"
            stock-symbol="%5EGSPC"
          ></market-index-box>
        </div>
        <div class="flex-auto">
          <market-index-box
            name="Dow 30"
            stock-symbol="%5EDJI"
          ></market-index-box>
        </div>
        <div class="flex-auto">
          <market-index-box
            name="Nasdaq"
            stock-symbol="%5EIXI"
          ></market-index-box>
        </div>
      </div>
    </div>
    <!-- End Right side row of info boxes -->
  </div>
  <!-- End First row of info -->
  <div class="flex">
    <div class="h-1/2"></div>
    <div class="h-1/2">
      <csv-chart
        v-bind:jsonObj="jsonObj"
        v-bind:totalValue="portfolioValue"
      ></csv-chart>
    </div>
  </div>
</template>

<script>
import PortfolioInfoBox from "./components/PortfolioInfoBox.vue";
import MarketIndexBox from "./components/MarketIndexBox.vue";
import CsvChart from "./components/CsvChart.vue";
import Navbar from "./components/Navbar.vue";
import csvToJson from "csvtojson";

export default {
  name: "App",
  data: function () {
    return {
      jsonObj: Object,
      data: "",
      portfolioValue: Number,
      overallReturn: Number,
      overallReturnPercentage: Number,
      todaysPerformance: Number,
      todaysPerformancePercentage: Number,
    };
  },
  components: {
    "portfolio-info-box": PortfolioInfoBox,
    "market-index-box": MarketIndexBox,
    "nav-bar": Navbar,
    "csv-chart": CsvChart,
  },
  methods: {
    generateJsonObj() {
      (async () => {
        this.jsonObj = await csvToJson().fromString(this.data);
        this.portfolioValue = this.getPortfolioValue();
        this.overallReturn = this.getOverallReturn();
        this.overallReturnPercentage = this.getOverallReturnPercentage();
        this.todaysPerformance = this.getTodaysPerformance();
        this.todaysPerformancePercentage = this.getTodaysPerformancePercentage();
        //console.log("Json", this.jsonObj);
      })();
    },
    getPortfolioValue() {
      var total = 0;
      for (let i = 0; i < this.jsonObj.length; i++) {
        total +=
          parseFloat(this.jsonObj[i]["Current Price"]) *
          this.jsonObj[i]["Quantity"];
      }
      return total;
    },
    getOverallReturn() {
      var totalReturn = 0;
      for (let i = 0; i < this.jsonObj.length; i++) {
        let stockReturn =
          parseFloat(this.jsonObj[i]["Current Price"]) -
          parseFloat(this.jsonObj[i]["Purchase Price"]);
        totalReturn += stockReturn * this.jsonObj[i]["Quantity"];
      }
      return totalReturn.toFixed(2);
    },
    getOverallReturnPercentage() {
      var purchasePrice = 0;
      for (let i = 0; i < this.jsonObj.length; i++) {
        purchasePrice +=
          parseFloat(this.jsonObj[i]["Purchase Price"]) *
          this.jsonObj[i]["Quantity"];
      }
      return ((this.overallReturn / purchasePrice) * 100).toFixed(2);
    },
    getTodaysPerformance() {
      var todayPriceChange = 0;
      for (let i = 0; i < this.jsonObj.length; i++) {
        todayPriceChange +=
          parseFloat(this.jsonObj[i]["Change"]) * this.jsonObj[i]["Quantity"];
      }
      return todayPriceChange.toFixed(2);
    },
    getTodaysPerformancePercentage() {
      return ((this.todaysPerformance / this.portfolioValue) * 100).toFixed(2);
    },
    updateData(e) {
      console.log("updatedata", e);
      this.data = String(e);
      this.generateJsonObj();
    },
  },
  created() {
    this.generateJsonObj();
  },
};
</script>
