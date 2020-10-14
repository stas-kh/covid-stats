<template>
  <v-container>
    <h1 class="mt-4">
      Covid stats
    </h1>

    <v-row>
      <v-col>
        <v-progress-circular
          v-if="loadingRegions"
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
                v-model="selectedRegion"
                item-value="iso"
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
                @click="setRegion"
              >
                Apply
              </v-btn>
            </v-col>
          </v-row>
        </v-form>
      </v-col>
    </v-row>

    <v-progress-circular
      v-if="loadingCases"
      :size="50"
      color="amber"
      indeterminate
    ></v-progress-circular>

    <daily-cases-chart
      v-if="!loadingCases && dailyCases.length > 0"
      :dates-range="datesRange"
      :daily-cases="dailyCases"
    />
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue'
import axios from 'axios';
import sortBy from 'lodash/sortBy';
import DailyCasesChart from "@/components/DailyCasesChart.vue";
import dayjs from "dayjs";
import sumBy from "lodash/sumBy";
import { Region } from "@/models/Region";
import { Report } from "@/models/Report";

export default Vue.extend({
  name: 'CovidStats',

  components: {
    DailyCasesChart
  },

  data: () => ({
    loadingRegions: false,
    loadingCases: false,
    regions: [] as Region[],
    dailyCases: [] as number[],
    datesRange: [] as string[],
    selectedRegion: null,
  }),

  created() {
    this.loadingRegions = true;

    this.datesRange = this.generateDatesRange(90);

    axios.get('regions')
      .then(({ data: items }) => {
        this.regions = sortBy(items.data, 'name');
      })
      .finally(() => {
        this.loadingRegions = false;
      });
  },

  methods: {
    generateDatesRange(nDays: number): string[] {
      const today = dayjs();
      const datesRange = [];

      for (let i = 1; i <= nDays; i++) {
        datesRange.push(today.subtract(i, 'd').format('YYYY-MM-DD'));
      }

      return datesRange;
    },
    parseDailyNewCases(report: { data: { data: Report[] } }[]): number[] {
      return report.map(({ data }) => sumBy(data.data, 'confirmed_diff'));
    },
    setRegion() {
      this.reset();

      Promise.all(this.datesRange.map((date: string) => {
        return axios.get('reports', {
          params: {
            iso: this.selectedRegion,
            date
          }
        })
      }))
        .then((results) => {
          this.dailyCases = this.parseDailyNewCases(results);
        })
        .finally(() => {
          this.loadingCases = false;
        })
    },
    reset() {
      this.loadingCases = true;
      this.dailyCases = [];
    }
  }
})
</script>

