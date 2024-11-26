<template>
  <main>
    <FormBuilder :form-config="formServices" />
  </main>
</template>

<script>
import FormBuilder from '../components/FormBuilder.vue'

export default {
  name: 'HomeView',
  components: { FormBuilder },
  data() {
    return {
      formServices: {}
    }
  },
  async created() {
    this.formServices = await this.loadFormConfig()
  },
  methods: {
    async loadFormConfig() {
      try {
        const response = await fetch('/form-config.json')

        if (!response.ok) {
          throw new Error(`Ошибка загрузки конфига: ${response.statusText}`)
        }

        const config = await response.json()

        return config
      } catch (error) {
        console.error('Ошибка загрузки конфига:', error)

        return {}
      }
    }
  }
}
</script>
