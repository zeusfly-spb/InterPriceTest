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
          :items="rawData"
          :headers="headers"
          :expanded.sync="expanded"
        >
          <template
              v-for="header in quoteHeaders"
              v-slot:[`header.${header.value}`]
          >
            <QuoteHeader
                :years="header.duration"
                :key="header.value"
            />
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
                    :key="period"
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
          <template v-slot:body.append>
            <tr>
              <td></td><td></td>
              <td>{{ `Average by ${currentMode}` }}</td>
              <template
                v-for="(header, index) in quoteHeaders"
              >
                <td
                  :key="`${header.value}${index}`"
                >
                  <div
                      class="quote-field"
                  >
                    <div>
                      {{ average({currency: currency, years: +header.duration, mode: currentMode, type: 'FIX'}) }}
                    </div>
                    <div>
                      {{ average({currency: currency, years: +header.duration, mode: currentMode, type: 'FRN'}) }}
                    </div>
                  </div>
                </td>
              </template>
            </tr>
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
    quoteHeaders () {
      const sortFn = (a, b) => a.duration - b.duration
      let result = []
      this.years.forEach(item => {
        if (this.shownYears.includes(item)) {
          const itemParams = item.split(' ')
          result.push({
            value: `col${itemParams.join('')}`,
            duration: +itemParams[0],
            sortable: false
          })
        }
      })
      result.sort(sortFn)
      return result
    },
    headers () {
      const stat = [
        {text: '', value: 'data-table-expand'},
        {text: 'DATE SENT', value: 'DateSent', width: '10em'},
        {text: 'COMPANY', value: 'Company', width: '20em'}
      ]
      return [...stat, ...this.quoteHeaders]
    },
    allQuotes () {
      let result = []
      this.rawData.forEach(data => {
        if (data.Quote) {
          data.Quote.forEach(quote => result.push(quote))
        }
      })
      return result
    }
  },
  methods: {
    average ({currency, years, type, mode}) {
      let result
      const check = quote => quote.Currency === currency && +quote.Years === years && quote.CouponType === type
      const adder = (a, b) => a + b[mode]
      const relevant = this.allQuotes.filter(quote => check(quote)).filter(item => +item[mode])
      const amount = relevant.reduce(adder, 0)
      const value = amount / relevant.length
      if (['Spread', '3MLSpread'].includes(mode)) {
        +value ? result = `+${Math.round(value)}bp` : null
      } else {
        +value ? result = `${value.toFixed(3)}%` : null
      }
      return result
    },
    companyYearsData ({id, years}) {
      const companyData = this.rawData.find(item => item.Id === id) || null
      return companyData && companyData.Quote && companyData.Quote
          .filter(item => item.Years === years && item.Currency === this.currency) || null
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
    years (val) {
      this.shownYears = val
    },
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