<script>
import { Line } from "vue3-chart-v2";

export default {
  name: "LineChart",
  extends: Line,
  props: ["chartDataset", "chartLabels"],
  data() {
    return {
      lineChartData: [],
      lineChartLabels: [],
    };
  },
  methods: {
    async renderChartData() {
      await this.sleep(1000);

      var dataCollection = {
        labels: this.lineChartLabels,
        datasets: [
          {
            label: "S&P 500",
            data: this.lineChartData,
            fill: true,
            borderColor: "#D6ED17FF",
            backgroundColor: "#101820FF",
            tension: 0.1,
          },
        ],
      };
      var options = {
        responsive: true,
        title: {
          display: true,
          text: "S&P 500",
          fontColor: "white",
        },
        legend: {
          display: true,
          labels: {
            fontColor: "white",
          },
        },
      };
      console.log("render linechart");
      this.renderChart(dataCollection, options);
    },
    sleep(ms) {
      return new Promise((resolve) => setTimeout(resolve, ms));
    },
  },
  mounted() {
    this.lineChartData = this.chartDataset;
    this.lineChartLabels = this.chartLabels;
    this.renderChartData();
  },
};
</script>