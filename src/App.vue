<template>
  <v-app>
    <v-app-bar
      app
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          :src="require('./assets/logo.png')"
          transition="scale-transition"
          width="120"
        />
      </div>

      <v-spacer />

      <v-btn
        href="https://www.datahow.ch/"
        target="_blank"
        text
      >
        <span class="mr-2">datahow.ch</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <covid-stats />
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue';
import CovidStats from './components/CovidStats.vue';

export default Vue.extend({
  name: 'App',

  components: {
    CovidStats,
  },
});
</script>
