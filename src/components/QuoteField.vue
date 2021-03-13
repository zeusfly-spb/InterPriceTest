<template>
  <td>
    <div
      class="quote-field"
    >
      <div>{{ typeValue('FIX') }}</div>
      <div>{{ typeValue('FRN') }}</div>
    </div>
  </td>

</template>

<script>
export default {
  name: 'QuoteField',
  props: {
    companyData: {
      type: Object,
      required: true
    },
    mode: {
      type: String,
      required: true
    }
  },
  methods: {
    typeValue (type) {
      let result = ''
      if (!this.companyData || this.companyData.CouponType !== type) {
        return result
      }
      const value =  this.companyData[this.mode] || null
      if (['Spread', '3MLSpread'].includes(this.mode)) {
        +value ? result = `+${value}bp` : null
      } else {
        +value ? result = `${value.toFixed(3)}%` : null
      }
      return result
    }
  }

}
</script>

<style scoped>
.quote-field {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
}
</style>