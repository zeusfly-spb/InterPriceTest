<template>
  <div class="mt-5 ml-5 mr-5">
    <v-row>
      <CurrencySwitcher
          v-model="currency"
      />
      <div class="mr-5"/>
      <YearsSwitcher
          v-model="years"
      />
    </v-row>
  </div>
</template>

<script>
import CurrencySwitcher from "@/components/CurrencySwitcher"
import YearsSwitcher from "@/components/YearsSwitcher"
export default {
  name: 'TableView',
  data: () =>  ({
    currency: 'USD',
    years: ['5 YRS']
  }),
  components: {
    CurrencySwitcher,
    YearsSwitcher
  }
}
</script>

<style scoped>

</style>