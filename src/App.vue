<template>
  <div id="app">
    <v-container :fluid="true" grid-list-md>
      <v-layout class="ma-6">
        <v-flex md3 :grow="true" class="ma-6 pa-6">
          <label for="initialAmount" class="mr-2"
            ><small>Initial Amount</small></label
          >
          <input
            id="initialAmount"
            name="initialAmount"
            type="number"
            placeholder="Initial Amount"
            v-model="initialAmount"
          />
        </v-flex>
        <v-flex md3>
          <label for="monthlyAmount" class="mr-2"
            ><small>Monthly Amount</small></label
          >
          <input
            id="monthlyAmount"
            name="monthlyAmount"
            type="number"
            placeholder="Monthly Amount"
            v-model="monthlyAmount"
          />
        </v-flex>
        <v-flex md3>
          <label for="timescaleInYears" class="mr-2"
            ><small>Timescale</small></label
          >
          <input
            id="timescaleInYears"
            name="timescaleInYears"
            type="number"
            placeholder="Timescale (In Years)"
            v-model="timescaleInYears"
          />
        </v-flex>
        <v-flex md3>
          <label for="targetAmount" class="mr-2"
            ><small>Target Amount</small></label
          >
          <input
            id="targetAmount"
            name="targetAmount"
            type="number"
            placeholder="Target Amount"
            v-model="targetAmount"
          />
        </v-flex>
      </v-layout>
      <v-layout class="ma-6">
        <v-flex md4>
          <v-btn :flat="true" id="lowRisk" @click="setLowRisk()"
            >Low Risk</v-btn
          >
        </v-flex>
        <v-flex md4>
          <v-btn :flat="true" id="medRisk" @click="setMediumRisk()"
            >Medium Risk</v-btn
          >
        </v-flex>
        <v-flex md4>
          <v-btn :flat="true" id="highRisk" @click="setHighRisk()"
            >High Risk</v-btn
          >
        </v-flex>
      </v-layout>
      <v-layout>
        <v-flex md12>
          <Chart :chart-options="chartOptions" />
        </v-flex>
      </v-layout>
    </v-container>
  </div>
</template>

<script>
import Chart from './components/Chart.vue'
import { Calculation } from './CalculationEngine'

export default {
  name: 'app',
  components: {
    Chart
  },
  data() {
    return {
      initialAmount: 2500,
      monthlyAmount: 150,
      timescaleInYears: 10,
      targetAmount: 10000
    }
  },
  computed: {
    engine() {
      return new Calculation.CalculationEngine(
        Number.parseInt(this.initialAmount),
        Number.parseInt(this.monthlyAmount),
        Number.parseInt(this.timescaleInYears)
      )
    },
    lowRiskResults() {
      return this.engine.lowRiskResults()
    },
    mediumRiskResults() {
      return this.engine.mediumRiskResults()
    },
    highRiskResults() {
      return this.engine.highRiskResults()
    },
    chartOptions() {
      {
        return {
          yAxis: {
            plotLines: [
              {
                value: this.targetAmount,
                color: 'green',
                width: 2,
                dashStyle: 'shortdash',
                label: {
                  text: 'Target'
                }
              }
            ],
            title: {
              text: 'Value (£)'
            }
          },
          xAxis: {
            tickInterval: 12,
            title: {
              text: 'Time (Years)'
            },
            labels: {
              formatter: function() {
                return this.value / 12
              }
            }
          },
          series: this.chartSeries
        }
      }
    },
    chartSeries() {
      {
        return [
          {
            type: 'line',
            name: 'Value',
            data: this.lowRiskResults.invested,
            color: 'green'
          },
          {
            type: 'line',
            name: 'Wide Band Upper',
            data: this.lowRiskResults.wideBandUpperLimit,
            color: 'blue',
            marker: {
              enabled: false
            }
          },
          {
            type: 'line',
            name: 'Wide Band Lower',
            data: this.lowRiskResults.wideBandLowerLimit,
            color: 'lightblue',
            marker: {
              enabled: false
            }
          },
          {
            type: 'line',
            name: 'Narrow Band Lower',
            color: 'orange',
            data: this.lowRiskResults.narrowBandUpperLimit,
            marker: {
              enabled: false
            }
          },
          {
            type: 'line',
            name: 'Narrow Band Lower',
            color: 'red',
            data: this.lowRiskResults.narrowBandLowerLimit,
            marker: {
              enabled: false
            }
          }
        ]
      }
    }
  },
  methods: {
    setHighRisk() {
      this.chartOptions.series[0].data = this.highRiskResults.invested
      ;(this.chartOptions.series[1].data = this.highRiskResults.wideBandUpperLimit),
        (this.chartOptions.series[2].data = this.highRiskResults.wideBandLowerLimit),
        (this.chartOptions.series[3].data = this.highRiskResults.narrowBandUpperLimit)
      this.chartOptions.series[4].data = this.highRiskResults.narrowBandLowerLimit
    },
    setMediumRisk() {
      this.chartOptions.series[0].data = this.mediumRiskResults.invested
      ;(this.chartOptions.series[1].data = this.mediumRiskResults.wideBandUpperLimit),
        (this.chartOptions.series[2].data = this.mediumRiskResults.wideBandLowerLimit),
        (this.chartOptions.series[3].data = this.mediumRiskResults.narrowBandUpperLimit)
      this.chartOptions.series[4].data = this.mediumRiskResults.narrowBandLowerLimit
    },
    setLowRisk() {
      this.chartOptions.series[0].data = this.lowRiskResults.invested
      ;(this.chartOptions.series[1].data = this.lowRiskResults.wideBandUpperLimit),
        (this.chartOptions.series[2].data = this.lowRiskResults.wideBandLowerLimit),
        (this.chartOptions.series[3].data = this.lowRiskResults.narrowBandUpperLimit)
      this.chartOptions.series[4].data = this.lowRiskResults.narrowBandLowerLimit
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
