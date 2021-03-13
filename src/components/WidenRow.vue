<template>
  <tr>
    <td></td><td></td>
    <td>{{ ownMode }}</td>
  </tr>
</template>

<script>
export default {
  name: 'WidenRow',
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
    allModes: ['Spread', 'Yield', '3MLSpread'],
    expandedData: null
  }),
  computed: {
    ownMode () {
      const remaining = this.allModes.filter(item => item !== this.currentMode)
      return remaining[this.position]
    }
  },
  methods: {
    setExpandedData (data) {
      console.log(`Received: ${data}`)
    }
  },
  created () {
    this.$eventHub.$emit(`getExpandedData`, this.commonData.Id)
    // this.$eventHub.$on(`expanded-${this.commonData.Id}`, this.getExpandedData)
  }
}
</script>

<style scoped>

</style>