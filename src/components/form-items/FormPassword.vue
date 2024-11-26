<template>
  <FormInput
    :label="label"
    type="password"
    @input="e => $emit('input', e)"
    @keydown="disableSpaces"
  />
</template>

<script>
import FormInput from '@/components/form-items/FormInput.vue'

export default {
  name: 'FormPassword',
  components: {
    FormInput
  },
  props: {
    label: {
      type: String,
      default: ''
    }
  },
  emits: ['input'],
  methods: {
    handleInput(e) {
      const value = e.target.value
      const trimmedValue = value.replace(/\s+/g, '')

      this.$emit('input', trimmedValue)
    },
    disableSpaces(e) {
      if (e.key === ' ') {
        e.preventDefault()
      }
    }
  }
}
</script>

<style lang="scss" scoped></style>
