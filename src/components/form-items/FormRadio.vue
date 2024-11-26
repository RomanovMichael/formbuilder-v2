<template>
  <fieldset>
    <legend>{{ label }}</legend>
    <div
      v-for="option in options"
      :key="option.value"
    >
      <input
        required
        type="radio"
        :name="name"
        :value="option.value"
        :checked="option.selected"
        @change="change"
      />
      <label :for="option.id">{{ option.text }}</label>
    </div>
  </fieldset>
</template>

<script>
export default {
  name: 'FormRadio',
  props: {
    label: {
      type: String,
      default: ''
    },
    options: {
      type: Array,
      required: true,
      validator(value) {
        if (!Array.isArray(value)) {
          console.error("Параметр 'options' должен быть массивом.")

          return false
        }

        return true
      }
    },
    name: {
      type: String,
      default: 'radio'
    }
  },
  emits: ['handle-change'],
  methods: {
    change(e) {
      const value = e.target.value

      this.$emit('handle-change', value)
    }
  }
}
</script>

<style scoped></style>
