<template>
  <div class="flex">
    <div class="w-2/3">
      <div class="flex space-x-4 mx-auto px-4">
        <div class="flex-1">
          <portfolio-info-box name="Performance Today"></portfolio-info-box>
        </div>
        <div class="flex-1">
          <portfolio-info-box name="Overall Return"></portfolio-info-box>
        </div>
        <div class="flex-1">
          <portfolio-info-box name="Portfolio Value"></portfolio-info-box>
        </div>
      </div>
    </div>
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
  </div>
</template>

<script>
import PortfolioInfoBox from "./components/PortfolioInfoBox.vue";
import MarketIndexBox from "./components/MarketIndexBox.vue";
export default {
  name: "App",
  components: {
    "portfolio-info-box": PortfolioInfoBox,
    "market-index-box": MarketIndexBox,
  },
};
</script>
