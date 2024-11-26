<template>
  <div>
    <p>{{ label }}</p>
    <input
      v-bind="$attrs"
      :type="type"
      required
      @input="input"
    />
  </div>
</template>

<script>
export default {
  name: 'FormInput',
  props: {
    label: {
      type: String,
      default: ''
    },
    type: {
      type: String,
      default: 'text'
    }
  },
  emits: ['handle-input'],
  methods: {
    input(e) {
      const value = e.target.value

      this.$emit('handle-input', value)
    }
  }
}
</script>

<style scoped></style>
