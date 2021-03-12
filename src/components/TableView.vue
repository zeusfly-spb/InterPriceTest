<template>
  <div class="mt-5 ml-5">
    <v-row>
      <CurrencySwitcher
          v-model="currency"
      />

    </v-row>

  </div>
</template>

<script>
import CurrencySwitcher from "@/components/CurrencySwitcher"
export default {
  name: 'TableView',
  data: () =>  ({
    currency: 'USD'
  }),
  components: {
    CurrencySwitcher
  }
}
</script>

<style scoped>

</style>