<template>
  <div class="form-builder">
    <form @submit.prevent="onSubmit($event.target)">
      <div
        v-for="(formSection, formSectionIndex) in formConfig"
        :key="formSection.name"
      >
        <h2>{{ formSection.name }}</h2>
        <div>
          <div
            v-for="formEl in formSection.items"
            :key="formEl.name"
          >
            <form-password
              v-if="formEl.type === 'password'"
              :label="formEl.label"
              :name="`${formSectionIndex}.${formEl.name}`"
            />
            <form-radio
              v-else-if="formEl.type === 'radio'"
              :label="formEl.label"
              :name="`${formSectionIndex}.${formEl.name}`"
              :options="formEl.additional.options"
            />
            <form-select
              v-else-if="formEl.type === 'select'"
              :label="formEl.label"
              :name="`${formSectionIndex}.${formEl.name}`"
              :options="formEl.additional.options"
            />
            <form-input
              v-else
              :label="formEl.label"
              :name="`${formSectionIndex}.${formEl.name}`"
            />
          </div>
        </div>
      </div>
      <button type="submit">Отправить</button>
      <button type="reset">Стереть</button>
    </form>
  </div>
</template>

<script>
import { validateFormConfig } from '@/validators'
import FormInput from '@/components/form-items/FormInput.vue'
import FormSelect from '@/components/form-items/FormSelect.vue'
import FormRadio from '@/components/form-items/FormRadio.vue'
import FormPassword from '@/components/form-items/FormPassword.vue'

export default {
  name: 'FormBuilder',
  components: { FormPassword, FormRadio, FormSelect, FormInput },
  props: {
    formConfig: {
      type: Object,
      required: true,
      validator: validateFormConfig
    }
  },
  created() {},
  methods: {
    onSubmit(form) {
      const rawData = this.collectFormData(form)
      const errors = this.validateForm(rawData)

      if (errors.length) {
        alert('Ошибки в форме:\n' + errors.join('\n'))

        return
      }

      const cleanedData = this.getCleanedData(rawData)

      this.sendData(cleanedData)
      form.reset()
    },
    collectFormData(form) {
      const formData = new FormData(form)

      return [...formData.entries()].reduce((acc, [key, value]) => {
        const [section, field] = key.split('.')
        acc[section] = acc[section] || {}
        acc[section][field] = value

        return acc
      }, {})
    },
    validateForm(data) {
      const errors = []

      for (const section in data) {
        const fields = data[section]

        for (const key in fields) {
          if (!fields[key]) {
            errors.push(`Поле "${key}" в секции "${section}" не заполнено.`)
          }

          if (key === 'repeat-pass' && fields['pass'] !== fields[key]) {
            errors.push(
              `Пароль и подтверждение не совпадают в секции "${section}".`
            )
          }
        }
      }

      return errors
    },
    getCleanedData(data) {
      const cleanedData = {}

      for (const section in data) {
        cleanedData[section] = {}

        for (const field in data[section]) {
          if (field == 'repeat-pass') continue

          cleanedData[section][field] =
            field === 'age'
              ? Number(data[section][field])
              : data[section][field]
        }
      }

      return cleanedData
    },
    async sendData(data) {
      try {
        const response = await fetch('/api', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(data)
        })

        if (!response.ok) {
          throw new Error(`Ошибка сервера: ${response.statusText}`)
        }

        const result = await response.json()
        console.log('Ответ сервера:', result)
      } catch (error) {
        console.error('Ошибка отправки:', error)
      } finally {
        console.log('Отправленные данные:', data)
      }
    }
  }
}
</script>

<style scoped></style>
