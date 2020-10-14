<template>
  <v-container>
    <apexchart
      width="1200"
      height="700"
      type="bar"
      :options="options"
      :series="series"
    ></apexchart>
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue';
import VueApexCharts from 'vue-apexcharts';

Vue.component('apexchart', VueApexCharts);

export default Vue.extend({
  name: 'DailyCasesChart',

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
        }
      }
    },
    series() {
      return [{
        name: 'Daily new cases',
        data: this.dailyCases,
      }];
    },
  },
})
</script>

