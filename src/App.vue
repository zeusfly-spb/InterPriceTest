<template>
  <v-app>
    <v-app-bar
      app
      dense
      color="#4e7091"
      dark
    >
      <div class="d-flex align-center">
        <h4>
          InterPriceTest
        </h4>
      </div>
    </v-app-bar>
    <v-main>
      <TableView
        :raw-data="jsonData"
      />
    </v-main>
    <v-footer
      :absolute="false"
      app
    >
      <span
        class="copyright"
      >
        Saifutdinov &copy; {{ new Date().getFullYear() }}
      </span>
    </v-footer>
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
<style>
.quote-field {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.centered {
  display: flex;
  align-items: center;
  align-content: center;
  justify-content: center;
  margin: .7em;
  width: 100%;
  height: 48px;
}
.copyright{
  color: #4e7091
}
.minimal {
  background-color: #f4f2d2
}
</style>
