<template>
  <v-container>
    <h1 class="mt-4">
      Covid stats
    </h1>

    <v-row>
      <v-col>
        <v-progress-circular
          v-if="isLoading"
          :size="50"
          color="amber"
          indeterminate
        ></v-progress-circular>

        <v-form
          v-else
        >
          <v-row>
            <v-col :cols="8">
              <v-autocomplete
                solo
                :items="regions"
                color="white"
                item-text="name"
                placeholder="Select a country"
              ></v-autocomplete>
            </v-col>

            <v-col cols="4">
              <v-btn
                large
                color="success"
              >
                Apply
              </v-btn>
            </v-col>
          </v-row>
        </v-form>
      </v-col>
    </v-row>

    <daily-cases-chart />
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue'
import axios from 'axios';
import sortBy from 'lodash/sortBy';
import DailyCasesChart from "@/components/DailyCasesChart.vue";

export default Vue.extend({
  name: 'CovidStats',

  components: {
    DailyCasesChart
  },

  data: () => ({
    isLoading: false,
    regions: [] as any[],
  }),

  created() {
    this.isLoading = true;

    axios.get('https://covid-api.com/api/regions')
      .then(({ data: items }) => {
        this.regions = sortBy(items.data, 'name');
      })
      .finally(() => {
        this.isLoading = false;
      })
  }
})
</script>

