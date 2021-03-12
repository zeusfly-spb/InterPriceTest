<template>
  <div>
    <MultiSwitch
        :options="options"
        @change="change"
    />
  </div>
</template>

<script>
import MultiSwitch from "@/components/MultiSwitch"
export default {
  name: 'CurrencySwitcher',
  data: () => ({
    options: {
      layout: {
        color: '#0D47A1',
        backgroundColor: 'white',
        selectedColor: 'white',
        selectedBackgroundColor: '#0D47A1',
        borderColor: '#0D47A1',
        fontWeight: 'normal',
      },
      size: {
        fontSize: 1,
        height: 2.5,
        width: 10,
      },
      items: {
        delay: .4,
        preSelected: 'USD',
        labels: [
          {name: 'USD'},
          {name: 'EUR'},
          {name: 'CAD'}
        ]
      }
    }
  }),
  methods: {
    change (event) {
      this.$emit('input', event.value)
    }
  },
  components: {
    MultiSwitch
  }
}
</script>

<style scoped>

</style>