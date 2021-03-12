<template>
  <div>
    <MultiSwitch
        :options="options"
        @change="change"
        multiple
    />
  </div>
</template>

<script>
import MultiSwitch from "@/components/MultiSwitch"
export default {
  name: 'YearsSwitcher',
  data: () => ({
    options: {
      size: {
        fontSize: 1,
        height: 2.5,
        width: 16,
      },
      items: {
        preSelected: ['5 YRS', '10 YRS', '40 YRS'],
        labels: [
          {name: '5 YRS'},
          {name: '10 YRS'},
          {name: '40 YRS'}
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
