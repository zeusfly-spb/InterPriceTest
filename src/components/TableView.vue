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
              <template
                v-for="(period, index) in periods"
              >
                <QuoteField
                    v-if="shownYears.includes(`${period} YRS`)"
                    :key="index"
                    :company-data="companyYearsData({company: item.Company, years: period})"
                    :mode="currentMode"
                />
              </template>
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
    periods: [5, 10, 40],
    currency: 'USD',
    currentMode: 'Spread',
    shownYears: []
  }),
  computed: {
    years () {
      return this.periods.map(item => `${item} YRS`)
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
          quoteHeaders.push({
            value: `col${itemParams.join('')}`,
            sortable: false,
            duration: +itemParams[0]
          })
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
  created () {
    this.shownYears = this.years
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