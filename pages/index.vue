<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <Bar
          v-if="loaded"
          :chart-options="chartOptions"
          :chart-data="chartData"
          :chart-id="chartId"
          :dataset-id-key="datasetIdKey"
          :plugins="plugins"
          :css-classes="cssClasses"
          :styles="styles"
        />
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import { Bar } from 'vue-chartjs/legacy'
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

export default {
  name: 'IndexPage',
  components: { Bar },
  props: {
    chartId: {
      type: String,
      default: 'bar-chart'
    },
    datasetIdKey: {
      type: String,
      default: 'label'
    },
    cssClasses: {
      default: '',
      type: String
    },
    styles: {
      type: Object,
      default: () => {}
    },
    plugins: {
      type: Object,
      default: () => {}
    }
  },
  data () {
    return {
      loaded: false,
      role: localStorage.getItem('role'),
      dashData: {},
      chartData: {
        labels: [1, 20],
        datasets: [{ data: [30, 40] }],
        backgroundColor: "rgba(20, 255, 0, 0.3)",
        borderColor: "rgba(100, 255, 0, 1)",
        borderWidth: 2,
      },
      chartOptions: {
        responsive: true
      }
    }
  },
  mounted () {
    this.getDashData()
  },
  methods: {
    async getDashData () {
      let data = []
      let labels = []
      const response = await this.$axios.$get('https://api.coindesk.com/v1/bpi/historical/close.json?start=2019-01-01&end=2019-12-31')
      const dashData = response.bpi
      data = []
      labels = []
      Object.keys(dashData).map(function(key) {
        labels.push(key);
        data.push(dashData[key])
      });
      this.chartData.labels = labels
      this.chartData.datasets[0].data = data
      this.loaded = true
    }
  }
}
</script>
