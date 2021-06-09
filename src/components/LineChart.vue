<script>
import { Line } from "vue3-chart-v2";

export default {
  name: "LineChart",
  extends: Line,
  props: ["chartDataset"],
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
  },
  mounted() {
    this.chartData = this.chartDataset;
    this.renderChart(this.chartData, this.lineChartOptions);
  },
};
</script>