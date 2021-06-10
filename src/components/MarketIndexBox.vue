<template>
  <div
    class="bg-black border-opacity-60 | p-1 border-solid rounded-2xl border-2 | flex justify-around cursor-pointer | hover:border-transparent | transition-colors duration-500"
    :class="{
      'hover:bg-green-400': positive,
      'border-green-400': positive,
      'hover:bg-red-600': !positive,
      'border-red-600': !positive,
    }"
  >
    <div v-if="!loaded" class="flex flex-col justify-center">
      <p class="text-base">{{ name }}</p>
      <p class="text-xs">
        <center>
          <svg
            class="animate-spin h-4 w-4 rounded-full bg-transparent border-2 border-transparent border-opacity-90"
            style="border-right-color: white; border-top-color: white"
            viewBox="0 0 24 24"
          ></svg>
        </center>
      </p>
    </div>
    <div v-else class="flex flex-col justify-center">
      <p class="text-base">{{ name }}</p>
      <p class="text-xs">{{ price.toLocaleString() }}</p>
      <p class="text-xs">
        {{ dayChange.toLocaleString() }}({{ dayPercentChange }})
      </p>
    </div>
  </div>
</template>

<script>
export default {
  props: ["name", "stockSymbol"],
  data() {
    return {
      price: Number,
      loaded: false,
      dayChange: Number,
      dayPercentChange: Number,
      positive: Boolean,
    };
  },
  methods: {
    async getIndexPrice(stockSymbol) {
      try {
        var data = await getMarketPrice(stockSymbol);
        this.price = data["price"]["regularMarketPrice"]["fmt"];
        this.dayChange = data["price"]["regularMarketChange"]["fmt"];
        this.dayPercentChange =
          data["price"]["regularMarketChangePercent"]["fmt"];
        this.positive = this.dayChange >= 0 ? true : false;
        this.loaded = true;
      } catch (error) {
        console.log(error);
        return "error loading";
      }
    },
  },
  created() {
    this.getIndexPrice(this.stockSymbol);
  },
};

function getMarketPrice(apiStockSymbol) {
  return new Promise(function (resolve) {
    let url =
      "https://apidojo-yahoo-finance-v1.p.rapidapi.com/stock/v2/get-summary?symbol=" +
      apiStockSymbol +
      "&region=US";

    fetch(url, {
      method: "GET",
      headers: {
        "x-rapidapi-key": "c4eac4392cmsh76076d1e061f713p1b7aa9jsn6f47c253ffd9",
        "x-rapidapi-host": "apidojo-yahoo-finance-v1.p.rapidapi.com",
      },
    })
      .then((response) => response.json())
      .then((data) => {
        //console.log(data);
        resolve(data);
      });
  });
}
</script>
  