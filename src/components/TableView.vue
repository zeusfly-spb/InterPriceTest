<template>
  <div class="mt-5 ml-5 mr-5">
    <v-row>
      <CurrencySwitcher
        v-model="currency"
      />
      <div class="mr-5"/>
      <YearsSwitcher
        v-model="shownYears"
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
            <QuoteHeader :years="5"/>
          </template>
          <template v-slot:header.col10YRS>
            <QuoteHeader :years="10"/>
          </template>
          <template v-slot:header.col40YRS>
            <QuoteHeader :years="40"/>
          </template>

          <template v-slot:item="{ item }">
            <tr>
              <td>
                {{ item.DateSent | moment('DD-MMM-YY')}}
              </td>
              <td>
                <strong>{{ item.Company }}</strong>
              </td>
              <QuoteField
                v-if="shownYears.includes('5 YRS')"
                :company-data="companyYearsData({company: item.Company, years: 5})"
                :mode="currentMode"
              />
              <QuoteField
                v-if="shownYears.includes('10 YRS')"
                :company-data="companyYearsData({company: item.Company, years: 10})"
                :mode="currentMode"
              />
              <QuoteField
                v-if="shownYears.includes('40 YRS')"
                :company-data="companyYearsData({company: item.Company, years: 40})"
                :mode="currentMode"
              />
            </tr>
          </template>
        </v-data-table>
      </v-card>
    </v-row>
  </div>
</template>

<script>
import QuoteField from "@/components/QuoteField"
import QuoteHeader from "@/components/QuoteHeader"
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
    periods: [5, 10, 40],
    modes: ['Spread', 'Yield', '3MLSpread'],
    currentMode: 'Spread',
    shownYears: ['5 YRS', '10 YRS', '40 YRS']
  }),
  computed: {
    years () {
      return this.periods.map(item => `${item} YRS`)
    },
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
  methods: {
    companyYearsData ({company, years}) {
      const companyData = this.rawData.find(item => item.Company === company) || null
      return companyData && companyData.Quote && companyData.Quote.find(item => item.Years === years) || null
    }
  },
  components: {
    CurrencySwitcher,
    YearsSwitcher,
    ModeSwitcher,
    QuoteHeader,
    QuoteField
  }
}
</script>

<style scoped>
</style>