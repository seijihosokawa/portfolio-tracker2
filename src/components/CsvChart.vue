
<template>
  <table class="rounded-t-lg m-5 w-full mx-auto">
    <tr class="text-left border-b-2 border-gray-300 text-center bg-opacity-0">
      <th class="px-3 py-3">Symbol</th>
      <th class="px-3 py-3">Current Price</th>
      <th class="px-3 py-3">Shares</th>
      <th class="px-3 py-3">Purchase Price</th>
      <th class="px-3 py-3">Change</th>
      <th class="px-3 py-3">% Change</th>
      <th class="px-3 py-3">Daily Gain/Loss</th>
      <th class="px-3 py-3">Total Gain/Loss</th>
      <th class="px-3 py-3">Market Value</th>
      <th class="px-3 py-3">Portfolio %</th>
    </tr>
    <tr
      v-for="item in formatedCsvData"
      :key="item.id"
      class="border-b border-gray-200 text-center"
      :class="{
        'bg-green-400': item.Change > 0,
        'bg-red-600': item.Change < 0,
      }"
    >
      <td class="px-3 py-3">{{ item["Symbol"] }}</td>
      <td class="px-3 py-3">${{ numberWithCommas(item["Current Price"]) }}</td>
      <td class="px-3 py-3">{{ item["Shares"] }}</td>
      <td class="px-3 py-3">${{ numberWithCommas(item["Purchase Price"]) }}</td>
      <td class="px-3 py-3">
        {{ item["Change"] }}
      </td>
      <td class="px-3 py-3">{{ item["Percent Change Today"] }}%</td>
      <td class="px-3 py-3">
        {{ item["Daily Gain/Loss"] }}
      </td>
      <td class="px-3 py-3">
        {{ numberWithCommas(item["Total Gain/Loss"]) }}
      </td>
      <td class="px-3 py-3">${{ numberWithCommas(item["Market Value"]) }}</td>
      <td class="px-3 py-3">{{ item["Portfolio Percent"] }}%</td>
    </tr>
  </table>
</template>
<script>
export default {
  props: ["jsonObj", "totalValue"],
  emits: ["getFormattedData"],
  data: function () {
    return {
      formatedCsvData: [],
    };
  },
  methods: {
    numberWithCommas(x) {
      return String(x).replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
    sortAlphaSymbol() {
      this.formatedCsvData.sort(function (a, b) {
        if (a.Symbol < b.Symbol) {
          return -1;
        }
        if (a.Symbol > b.Symbol) {
          return 1;
        }
        return 0;
      });
    },
    returnFormattedData() {
      //console.log(this.formatedCsvData);
      this.$emit("get-formatted-data", this.formatedCsvData);
    },
  },
  beforeUpdate() {
    this.formatedCsvData = [];
    for (const i in this.jsonObj) {
      let target_copy = Object.assign({}, this.jsonObj)[i];
      let stock = {
        Symbol: target_copy["Symbol"],
        "Current Price": target_copy["Current Price"],
        Shares: parseInt(target_copy["Quantity"]),
        "Purchase Price": target_copy["Purchase Price"],
        Change: parseFloat(target_copy["Change"]).toFixed(2),
        "Percent Change Today": (
          (parseFloat(target_copy["Change"]).toFixed(2) /
            parseFloat(target_copy["Current Price"]).toFixed(2)) *
          100
        ).toFixed(2),
        "Daily Gain/Loss": (
          parseFloat(target_copy["Change"]).toFixed(2) * target_copy["Quantity"]
        ).toFixed(2),
        "Total Gain/Loss": (
          (parseFloat(target_copy["Current Price"]) -
            parseFloat(target_copy["Purchase Price"])) *
          target_copy["Quantity"]
        ).toFixed(2),
        "Market Value": (
          parseFloat(target_copy["Current Price"]) * target_copy["Quantity"]
        ).toFixed(2),
        "Portfolio Percent": (
          ((
            parseFloat(target_copy["Current Price"]) * target_copy["Quantity"]
          ).toFixed(2) /
            this.totalValue) *
          100
        ).toFixed(2),
      };
      this.formatedCsvData.push(stock);
    }
    this.sortAlphaSymbol();
    this.returnFormattedData();
  },
};
</script>