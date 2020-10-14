<template>
  <v-container>
    <v-slider
      v-model="movingAverage"
      label="Moving average"
      thumb-label="always"
      :min="1"
      :max="90"
    ></v-slider>

    <apexchart
      width="1200"
      height="700"
      :options="options"
      :series="series"
    ></apexchart>
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue';
import VueApexCharts from 'vue-apexcharts';
import sma from 'sma';

Vue.component('apexchart', VueApexCharts);

export default Vue.extend({
  name: 'DailyCasesChart',

  data: () => ({
    movingAverage: 8,
  }),

  props: {
    dailyCases: {
      type: Array,
      default: () => [],
    },
    datesRange: {
      type: Array,
      default: () => [],
    },
  },

  computed: {
    options() {
      return {
        chart: {
          id: 'daily-cases'
        },
        xaxis: {
          categories: this.datesRange,
        },
        stroke: {
          width: [0, 2]
        },
        colors: ['#f57c00', '#88b5dd'],
        tooltip: {
          enabled: true,
          shared: false,
          intersect: true,
          x: {
            show: false,
          },
          y: {
            formatter(value: string) {
              return parseInt(value);
            },
          },
        },
        yaxis: {
          labels: {
            formatter(value: string) {
              return parseInt(value);
            },
          },
          title: {
            text: 'Total daily new cases of the selected country',
          },
        },
      }
    },
    series() {
      return [{
        type: 'column',
        name: 'Daily new cases',
        data: this.dailyCases,
      }, {
        type: 'line',
        name: 'Moving average',
        data: sma(this.dailyCases, this.movingAverage)
      }];
    },
  },
})
</script>

