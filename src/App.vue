<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <span>
          InterPriceTest
        </span>
      </div>
    </v-app-bar>
    <v-main>
      <TableView
        :raw-data="jsonData"
      />
    </v-main>
  </v-app>
</template>

<script>
import jsonData from "./data.json"
import TableView from "@/components/TableView"

export default {
  name: 'App',
  data: () => ({
    jsonData: jsonData.Items
  }),
  components: {
    TableView
  }
}
</script>
