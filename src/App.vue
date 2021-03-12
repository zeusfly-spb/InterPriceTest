<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <span
          class="mr-2"
        >
          InterPriceTest
        </span>
      </div>
    </v-app-bar>

    <v-main>
      <HelloWorld/>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';
import jsonData from "./data.json"

export default {
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    jsonData: jsonData.Items
  }),
};
</script>
