<template>
  <td>
    <div
      class="quote-field"
    >
      <div
        class="centered"
      >
        {{ typeValue('FIX') }}
      </div>
      <div
        class="centered"
      >
        {{ typeValue('FRN') }}
      </div>
    </div>
  </td>

</template>

<script>
export default {
  name: 'QuoteField',
  props: {
    companyData: {
      type: Array
    },
    mode: {
      type: String,
      required: true
    }
  },
  data: () => ({
    activeCurrency: null
  }),
  methods: {
    setActiveCurrency (currency) {
      this.activeCurrency = currency
    },
    typeValue (type) {
      let result
      const relevant = this.companyData && this.companyData.find(item => item.CouponType === type) || null
      const value = relevant && relevant[this.mode] || null
      if (['Spread', '3MLSpread'].includes(this.mode)) {
        +value ? result = `+${value}bp` : null
      } else {
        +value ? result = `${value.toFixed(3)}%` : null
      }
      return result
    }
  },
  created () {
    this.$eventHub.$on(`activeCurrencyBroadcast`, this.setActiveCurrency)
    this.$eventHub.$emit(`activeCurrencyRequest`)
  }
}
</script>

<style scoped>

</style>