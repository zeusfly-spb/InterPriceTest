<template>
  <div>
    <v-icon
      @click="toggle"
    >
      {{ expanded ? 'mdi-chevron-up' : 'mdi-chevron-down'}}
    </v-icon>
  </div>
</template>

<script>
export default {
  name: 'ExpandIcon',
  props: {
    value: {
      type: Array,
      required: true
    },
    item: {
      type: Object,
      required: true
    }
  },
  computed: {
    expanded () {
      return !!this.value.find(item => item.Id === this.item.Id)
    }
  },
  methods: {
    toggle () {
      this.$emit('input', this.expanded ? this.value.filter(item => item.Id !== this.item.Id) : [...this.value, this.item])
    }
  }
}
</script>

<style scoped>

</style>