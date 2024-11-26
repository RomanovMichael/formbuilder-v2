<template>
  <div>
    <p>{{ label }}</p>
    <select
      :name="name"
      @change="select"
    >
      <option
        v-for="option in options"
        :key="option.value"
        :selected="option.selected"
      >
        {{ option.text }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: 'FormSelect',
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
      required: true
    }
  },
  emits: ['handle-select'],
  methods: {
    select(e) {
      const value = e.target.value

      this.$emit('handle-select', value)
    }
  }
}
</script>

<style scoped></style>
