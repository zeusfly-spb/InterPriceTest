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
      <div class="mr-5"/>
      <ModeSwitcher
        v-model="currentMode"
      />
    </v-row>
  </div>
</template>

<script>
import CurrencySwitcher from "@/components/CurrencySwitcher"
import YearsSwitcher from "@/components/YearsSwitcher"
import ModeSwitcher from "@/components/ModeSwitcher"
export default {
  name: 'TableView',
  data: () =>  ({
    currency: 'USD',
    years: ['5 YRS', '10 YRS', '40 YRS'],
    modes: ['Spread', 'Yield', '3MLSpread'],
    currentMode: 'Spread'
  }),
  components: {
    CurrencySwitcher,
    YearsSwitcher,
    ModeSwitcher
  }
}
</script>

<style scoped>

</style>