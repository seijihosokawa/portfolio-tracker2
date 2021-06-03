
<script>
import { Line } from "vue3-chart-v2";

export default {
  name: "LineChart",
  extends: Line,
  props: ["chartDataset", "chartLabels"],
  data() {
    return {};
  },
  methods: {
    renderChartData() {
      console.log("render");
      var dataCollection = {
        labels: this.chartLabels,
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
          text: "Stocks % of Portfolio",
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
  mounted() {
    this.renderChartData();
  },
};
</script>