<template>
  <tr>
    <td></td><td></td>
    <td>{{ ownMode }}</td>
  </tr>
</template>

<script>
export default {
  name: 'WidenRows',
  props: {
    commonData: {
      type: Object,
      required: true
    },
    currentMode: {
      type: String,
      required: true
    },
    position: {
      type: Number,
      required: true
    }
  },
  data: () => ({
    allModes: ['Spread', 'Yield', '3MLSpread']
  }),
  computed: {
    ownMode () {
      const remaining = this.allModes.filter(item => item !== this.currentMode)
      return remaining[this.position]
    }
  }
}
</script>

<style scoped>

</style>