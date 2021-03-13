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
          show-expand
          item-key="Id"
          :items="rows"
          :headers="headers"
          :expanded.sync="expanded"
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
                <ExpandIcon
                  v-model="expanded"
                  :item="item"
                />
              </td>
              <td>
                {{ item.DateSent | moment('DD-MMM-YY') }}
              </td>
              <td>
                <strong>{{ item.Company }}</strong>
              </td>
              <template
                v-for="period in periods"
              >
                <QuoteField
                    v-if="shownYears.includes(`${period} YRS`)"
                    :key="period.Id"
                    :company-data="companyYearsData({id: item.Id, years: period})"
                    :mode="currentMode"
                />
              </template>
            </tr>
          </template>
          <template v-slot:expanded-item="{ item }">
            <template
              v-for="(pos, index) in [0, 1]"
            >
              <WidenRow
                  :shown-years="shownYears"
                  :key="`expanded${index + 10 * Math.random()}`"
                  :common-data="item"
                  :current-mode="currentMode"
                  :position="pos"
              />
            </template>
            </template>
        </v-data-table>
      </v-card>
    </v-row>
  </div>
</template>

<script>
import WidenRow from "@/components/WidenRow"
import ExpandIcon from "@/components/ExpandIcon"
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
    expanded: [],
    modes: ['Spread', 'Yield', '3MLSpread'],
    // periods: [5, 10, 40],
    currency: 'USD',
    currentMode: 'Spread',
    shownYears: []
  }),
  computed: {
    periods () {
      let periods = []
      this.rawData.forEach(item => {
        if (item.Quote) {
          item.Quote.forEach(quote => quote.Currency === this.currency ? periods.push(+quote.Years) : null)
        }
      })
      return [... new Set(periods)]
    },
    years () {
      return this.periods.map(item => `${item} YRS`)
    },
    headers () {
      const stat = [
        {text: '', value: 'data-table-expand'},
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
    companyYearsData ({id, years}) {
      const companyData = this.rawData.find(item => item.Id === id) || null
      return companyData && companyData.Quote && companyData.Quote.find(item => item.Years === years) || null
    },
    sendExpandedData (id) {
      const sortFn = (a, b) => a.period - b.period
      const expandedData = this.shownYears.map(title => +title.split(' ')[0])
          .map(period => ({
            period: period,
            yearsData: this.companyYearsData({id: id, years: period
            })}))
          .sort(sortFn)
      this.$eventHub.$emit(`expandedDataResponse${id}`, expandedData)
    },
    broadcastActiveCurrency () {
      this.$eventHub.$emit('activeCurrencyBroadcast', this.currency)
    },
    periodsBroadcast () {
      this.$eventHub.$emit('periodsBroadcast', this.periods)
    }
  },
  created () {
    this.shownYears = this.years
    this.$eventHub.$on('expandedDataRequest', this.sendExpandedData)
    this.$eventHub.$on('activeCurrencyRequest', this.broadcastActiveCurrency)
    this.$eventHub.$on('periodsRequest', this.periodsBroadcast)
  },
  watch: {
    currency () {
      this.broadcastActiveCurrency()
    },
    periods () {
      this.periodsBroadcast()
    }
  },
  components: {
    CurrencySwitcher,
    YearsSwitcher,
    ModeSwitcher,
    QuoteHeader,
    QuoteField,
    ExpandIcon,
    WidenRow
  }
}
</script>

<style scoped>
</style>