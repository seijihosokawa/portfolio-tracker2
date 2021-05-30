
<template>
  <table class="rounded-t-lg m-5 w-5/6 mx-auto bg-gray-200 text-gray-800">
    <tr class="text-left border-b-2 border-gray-300">
      <th class="px-4 py-3">Symbol</th>
      <th class="px-4 py-3">Current Price</th>
      <th class="px-4 py-3">Shares</th>
      <th class="px-4 py-3">Purchase Price</th>
      <th class="px-4 py-3">Change</th>
      <th class="px-4 py-3">% Change Today</th>
      <th class="px-4 py-3">Daily Gain/Loss</th>
      <th class="px-4 py-3">Total Gain/Loss</th>
      <th class="px-4 py-3">Market Value</th>
      <th class="px-4 py-3">Portfolio %</th>
    </tr>
    <tr
      v-for="item in jsonObj"
      :key="item.id"
      class="bg-gray-100 border-b border-gray-200"
    >
      <td class="px-4 py-3">{{ item["Symbol"] }}</td>
      <td class="px-4 py-3">${{ numberWithCommas(item["Current Price"]) }}</td>
      <td class="px-4 py-3">{{ parseInt(item["Quantity"]) }}</td>
      <td class="px-4 py-3">${{ numberWithCommas(item["Purchase Price"]) }}</td>
      <td class="px-4 py-3">{{ parseFloat(item["Change"]).toFixed(2) }}</td>
      <td class="px-4 py-3">
        {{
          (
            (parseFloat(item["Change"]).toFixed(2) /
              parseFloat(item["Current Price"]).toFixed(2)) *
            100
          ).toFixed(2)
        }}%
      </td>
      <td class="px-4 py-3">
        {{
          (
            parseFloat(item["Change"]).toFixed(2) * parseInt(item["Quantity"])
          ).toFixed(2)
        }}
      </td>
      <td class="px-4 py-3">
        {{
          numberWithCommas(
            (
              (parseFloat(item["Current Price"]) -
                parseFloat(item["Purchase Price"])) *
              parseInt(item["Quantity"])
            ).toFixed(2)
          )
        }}
      </td>
      <td class="px-4 py-3">
        ${{
          numberWithCommas(
            (
              parseFloat(item["Current Price"]) * parseInt(item["Quantity"])
            ).toFixed(2)
          )
        }}
      </td>
      <td class="px-4 py-3">
        {{
          (
            ((
              parseFloat(item["Current Price"]) * parseInt(item["Quantity"])
            ).toFixed(2) /
              totalValue) *
            100
          ).toFixed(2)
        }}%
      </td>
    </tr>
  </table>
</template>
<script>
export default {
  props: ["jsonObj", "totalValue"],
  data: function () {
    return {};
  },
  methods: {
    numberWithCommas(x) {
      return String(x).replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
  },
  created() {},
};
</script>