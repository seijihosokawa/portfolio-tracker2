
<script>
import { Pie } from "vue3-chart-v2";

export default {
  name: "PieChart",
  extends: Pie,
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
            backgroundColor: [
              "#0074D9",
              "#FF4136",
              "#2ECC40",
              "#FF851B",
              "#7FDBFF",
              "#B10DC9",
              "#FFDC00",
              "#001f3f",
              "#39CCCC",
              "#01FF70",
              "#85144b",
              "#F012BE",
              "#3D9970",
              "#111111",
              "#AAAAAA",
            ],
            data: this.chartDataset,
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