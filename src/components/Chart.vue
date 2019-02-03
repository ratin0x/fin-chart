<template>
  <div id="chart">
    <highchart :options="chartOptions" />
  </div>
</template>
<script>
import { Chart } from 'highcharts-vue'
export default {
  name: 'Chart',
  props: {
    chartOptions: Object
  },
  components: {
    highchart: Chart
  }
}
</script>

<style scoped lang="scss"></style>
