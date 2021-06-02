
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
            label: "Light",
            backgroundColor: "#D7F2C4",
            data: this.chartDataset,
          },
        ],
      };
      this.renderChart(dataCollection);
    },
  },
  mounted() {
    this.renderChartData();
  },
};
</script>