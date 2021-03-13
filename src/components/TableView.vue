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
      <v-card width="100vw">
        <v-data-table
            hide-default-footer
            :items="rows"
            :headers="headers"
        >
          <template v-slot:header.col5YRS>
            <div
              class="quote-header"
            >
              <div
                class="top-header"
              >
                <strong>5 YRS</strong>
              </div>
              <div
                class="bottom-header"
              >
                <div>FIX</div>
                <div>FRN</div>
              </div>
            </div>
          </template>
          <template v-slot:header.col10YRS>
            <div
                class="quote-header"
            >
              <div
                  class="top-header"
              >
                <strong>10 YRS</strong>
              </div>
              <div
                  class="bottom-header"
              >
                <div>FIX</div>
                <div>FRN</div>
              </div>
            </div>
          </template>
          <template v-slot:header.col40YRS>
            <div
                class="quote-header"
            >
              <div
                  class="top-header"
              >
                <strong>40 YRS</strong>
              </div>
              <div
                  class="bottom-header"
              >
                <div>FIX</div>
                <div>FRN</div>
              </div>
            </div>
          </template>

          <template v-slot:item="{ item }">
            <tr>
              <td>
                {{ item.DateSent | moment('DD-MMM-YY')}}
              </td>
              <td>
                <strong>{{ item.Company }}</strong>
              </td>
            </tr>
          </template>
        </v-data-table>
      </v-card>


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
    currentMode: 'Spread',
    shownYears: ['5 YRS', '10 YRS', '40 YRS']
  }),
  computed: {
    presentYears () {
      let present = []
      this.rawData.forEach(item => {
        if (item.Quote) {
          item.Quote.forEach(quote => present.push(quote.Years))
        }
      })
      return [... new Set(present)]
    },
    headers () {
      const stat = [
        {text: 'DATE SENT', value: 'DateSent', width: '10em'},
        {text: 'COMPANY', value: 'Company', width: '20em'}
      ]
      let quoteHeaders = []
      this.years.forEach(item => {
        if (this.shownYears.includes(item)) {
          const itemParams = item.split(' ')
          quoteHeaders.push({value: `col${itemParams.join('')}`, duration: +itemParams[0]})
        }
      })
      quoteHeaders.sort((a, b) => a.duration - b.duration)
      return [...stat, ...quoteHeaders]
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
.bottom-header {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-around;

}
.top-header {
  text-align: center;
  border-bottom: 1px solid black;
}
.quote-header {
  display: flex;
  flex-direction: column;
}
</style>