<script>
import { Object } from "core-js";
import { Line } from "vue3-chart-v2";

export default {
  name: "LineChart",
  extends: Line,
  props: ["chartDataset", "chartLabels", "dateRange"],
  data() {
    return {
      lineChartLabels: [],
      timeStamps: [],
      closingPrices: [],
    };
  },
  methods: {
    async getApiDataHandler() {
      var data = await this.getApiData();
      console.log(data);
      this.timeStamps = data.timestamp;
      this.closingPrices = data.indicators.quote[0].close;

      console.log(this.timeStamps);
      console.log(this.closingPrices);
    },
    getApiData() {
      var interval = "15m";
      var dateRange = this.dateRange;
      console.log("getting api data");
      return new Promise(function (resolve) {
        fetch(
          `https://apidojo-yahoo-finance-v1.p.rapidapi.com/market/get-charts?symbol=AApl&interval=${interval}&range=${dateRange}&region=US`,
          {
            method: "GET",
            headers: {
              "x-rapidapi-key":
                "c4eac4392cmsh76076d1e061f713p1b7aa9jsn6f47c253ffd9",
              "x-rapidapi-host": "apidojo-yahoo-finance-v1.p.rapidapi.com",
            },
          }
        )
          .then((response) => response.json())
          .then((data) => {
            resolve(data.chart.result[0]);
          })
          .catch((err) => {
            console.error(err);
          });
      });
    },
    renderChartData() {
      console.log(this.timeStamps);
      console.log(this.closingPrices);

      var dataCollection = {
        labels: [1, 2, 3, 4, 5, 6, 7],
        datasets: [
          {
            label: "My First Dataset",
            data: [65, 59, 80, 81, 56, 55, 40],
            fill: false,
            borderColor: "rgb(75, 192, 192)",
            tension: 0.1,
          },
        ],
      };
      var options = {
        responsive: true,
        title: {
          display: true,
          text: "Portfolio vs S&P 500",
          fontColor: "white",
        },
        legend: {
          display: true,
          labels: {
            fontColor: "white",
          },
        },
      };
      this.renderChart(dataCollection, options);
    },
  },
  created() {
    this.getApiDataHandler();
  },
  mounted() {
    this.renderChartData();
  },
};
</script>