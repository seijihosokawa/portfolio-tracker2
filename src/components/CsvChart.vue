
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
    sortByAlpha() {
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
    const handler = {
      get(target, property) {
        return target[property];
      },
    };
    const proxy = new Proxy(this.jsonObj, handler);
    //generate an array of objects with correct formatting
    for (const i in proxy) {
      let stock = {
        Symbol: proxy[i]["Symbol"],
        "Current Price": proxy[i]["Current Price"],
        Shares: parseInt(proxy[i]["Quantity"]),
        "Purchase Price": proxy[i]["Purchase Price"],
        Change: parseFloat(proxy[i]["Change"]).toFixed(2),
        "Percent Change Today": (
          (parseFloat(proxy[i]["Change"]).toFixed(2) /
            parseFloat(proxy[i]["Current Price"]).toFixed(2)) *
          100
        ).toFixed(2),
        "Daily Gain/Loss": (
          parseFloat(proxy[i]["Change"]).toFixed(2) * proxy[i]["Quantity"]
        ).toFixed(2),
        "Total Gain/Loss": (
          (parseFloat(proxy[i]["Current Price"]) -
            parseFloat(proxy[i]["Purchase Price"])) *
          proxy[i]["Quantity"]
        ).toFixed(2),
        "Market Value": (
          parseFloat(proxy[i]["Current Price"]) * proxy[i]["Quantity"]
        ).toFixed(2),
        "Portfolio Percent": (
          ((
            parseFloat(proxy[i]["Current Price"]) * proxy[i]["Quantity"]
          ).toFixed(2) /
            this.totalValue) *
          100
        ).toFixed(2),
      };
      this.formatedCsvData.push(stock);
    }
    this.sortByAlpha();
    this.returnFormattedData();
  },
};
</script>