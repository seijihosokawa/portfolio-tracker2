<script>
import { Line } from "vue3-chart-v2";

export default {
  name: "LineChart",
  extends: Line,
  props: ["chartDataset", "chartLabels"],
  data() {
    return {
      chartData: {},
      lineChartOptions: {
        responsive: true,
        legend: {
          display: true,
          labels: {
            fontColor: "white",
          },
        },
      },
    };
  },
  watch: {
    chartDataset(newChartData) {
      this.chartData = newChartData;
    },
    chartLabels(newChartLabels) {
      //console.log(newChartLabels);
      this.chartData.labels = newChartLabels;
    },
  },
  mounted() {
    this.chartData = this.chartDataset;
    this.renderChart(this.chartData, this.lineChartOptions);
  },
};
</script>