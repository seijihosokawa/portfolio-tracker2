<script>
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
      return data;
    },
    getApiData() {
      var interval = "15m";
      if (this.dateRange === "5d") interval = "1d";
      if (this.dateRange === "1m") interval = "1w";
      if (this.dateRange === "3m" || this.dateRange === "6m") interval = "1m";
      var dateRange = this.dateRange;
      //other token: c4eac4392cmsh76076d1e061f713p1b7aa9jsn6f47c253ffd9
      console.log("getting api data");
      return new Promise(function (resolve) {
        fetch(
          `https://apidojo-yahoo-finance-v1.p.rapidapi.com/market/get-charts?symbol=%5EGSPC&interval=${interval}&range=${dateRange}&region=US&comparisons=AAPL%2CTSLA%2CAMD%2CSQ`,
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
            resolve(data.chart.result[0]);
          })
          .catch((err) => {
            console.error(err);
          });
      });
    },
    renderChartData() {
      this.getApiDataHandler().then((data) => {
        this.timeStamps = data.timestamp;
        this.closingPrices = data.indicators.quote[0].close;
        console.log(this.timeStamps);
        console.log(this.closingPrices);
        this.timeStamps.forEach(function (val, index, arr) {
          arr[index] = new Date(val * 1000).toLocaleString().split(",")[1];
        });
        console.log(this.timeStamps);

        var dataCollection = {
          labels: this.timeStamps,
          datasets: [
            {
              label: "S&P 500",
              data: this.closingPrices,
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
      });
    },
  },
  mounted() {
    this.renderChartData();
  },
};
</script>