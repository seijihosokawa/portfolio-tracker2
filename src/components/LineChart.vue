
<script>
import { Pie } from "vue3-chart-v2";

export default {
  extends: Pie,
  data: function () {
    return {
      chartLabels: [],
      chartPercentiles: [],
    };
  },
  props: ["chartdata"],
  methods: {
    generateChartLabels() {
      console.log("generateChartLabels", this.chartdata);
      for (const i in this.chartdata) {
        let target_copy = Object.assign({}, this.chartdata)[i];
        this.chartLabels.push(target_copy["Symbol"]);
        this.chartPercentiles.push(target_copy["Portfolio Percent"]);
      }
    },
  },
  updated() {
    this.generateChartLabels();
    this.renderChart({
      labels: this.chartLabels,
      datasets: [
        {
          data: this.chartPercentiles,
        },
      ],
    });
  },
  mounted() {
    this.renderChart({
      labels: ["Stocks"],
      datasets: [
        {
          data: [100],
        },
      ],
    });
  },
};
</script>
