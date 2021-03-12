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
    <v-row>
      <v-data-table
        hide-default-footer
        :items="rows"
        :headers="headers"
      >
        <template v-slot:item="{ item }">
          <tr>
            <td>
              {{ item.DateSent }}
            </td>
            <td>
              {{ item.Company }}
            </td>
          </tr>
        </template>
      </v-data-table>

    </v-row>
  </div>
</template>

<script>
import CurrencySwitcher from "@/components/CurrencySwitcher"
import YearsSwitcher from "@/components/YearsSwitcher"
import ModeSwitcher from "@/components/ModeSwitcher"
export default {
  name: 'TableView',
  props:{
    rawData: {
      type: Array,
      required: true
    }
  },
  data: () =>  ({
    currency: 'USD',
    years: ['5 YRS', '10 YRS', '40 YRS'],
    modes: ['Spread', 'Yield', '3MLSpread'],
    currentMode: 'Spread'
  }),
  computed: {
    headers () {
      const stat = [
        {text: 'DATE SENT', value: 'DateSent'},
        {text: 'COMPANY', value: 'Company'}
      ]
      return stat
    },
    rows () {
      return this.rawData
    }
  },
  components: {
    CurrencySwitcher,
    YearsSwitcher,
    ModeSwitcher
  }
}
</script>

<style scoped>

</style>