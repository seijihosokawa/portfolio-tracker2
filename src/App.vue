<template>
  <!-- First row of info -->
  <div class="flex">
    <!--  Left side row of info boxes -->
    <div class="w-2/3">
      <div class="flex space-x-4 mx-auto px-4">
        <div class="flex-1">
          <portfolio-info-box name="Performance Today"></portfolio-info-box>
        </div>
        <div class="flex-1">
          <portfolio-info-box
            name="Overall Return"
            v-bind:value="totalReturn"
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
    <csv-chart></csv-chart>
  </div>
</template>

<script>
import PortfolioInfoBox from "./components/PortfolioInfoBox.vue";
import MarketIndexBox from "./components/MarketIndexBox.vue";
import CsvChart from "./components/CsvChart.vue";
import data from "./quotes.csv";
import csvToJson from "csvtojson";

export default {
  name: "App",
  data: function () {
    return {
      jsonObj: Object,
      portfolioValue: Number,
      overallReturn: Number,
      overallReturnPercentage: Number,
    };
  },
  components: {
    "portfolio-info-box": PortfolioInfoBox,
    "market-index-box": MarketIndexBox,
    "csv-chart": CsvChart,
  },
  methods: {
    generateJsonObj() {
      (async () => {
        this.jsonObj = await csvToJson().fromString(data);
        //console.log(json);
        this.getPortfolioValue();
        this.getOverallReturn();
      })();
    },
    getPortfolioValue() {
      //console.log(this.jsonObj);
      var total = 0;
      for (let i = 0; i < this.jsonObj.length; i++) {
        total +=
          parseFloat(this.jsonObj[i]["Current Price"]) *
          parseInt(this.jsonObj[i]["Quantity"]);
      }
      this.portfolioValue = total;
      this.port;
    },
    getOverallReturn() {
      var totalReturn = 0;
      for (let i = 0; i < this.jsonObj.length; i++) {
        let stockReturn =
          parseFloat(this.jsonObj[i]["Current Price"]) -
          parseFloat(this.jsonObj[i]["Purchase Price"]);
        console.log(stockReturn);
        totalReturn += stockReturn * parseInt(this.jsonObj[i]["Quantity"]);
      }
      this.overallReturn = totalReturn;
    },
  },
  created() {
    this.generateJsonObj();
  },
};
</script>
