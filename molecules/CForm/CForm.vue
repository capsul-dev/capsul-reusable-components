<template>
  <div class="grid gap-y-4 py-2 px-2">
    <!-- form -->
    <div
      v-for="([key, field], index) in Object.entries(fields)"
      :key="`field-${index}`"
    >

    <c-input v-if="['text', 'password', 'number'].includes(field.type)" :type="field.type" :placeholder="field.placeholder" v-model="formData[key]">
      {{ field.label }}
    </c-input>

    <!-- checkbox and radio -->
    <div v-else-if="['checkbox', 'radio'].includes(field.type)">
      <header class="text-sm py-2">{{ field.label }}</header>
      <c-checkbox v-if="field.type === 'checkbox'" v-for="(value, vindex) in field.values" :key="`value-${vindex}`" v-model="formData[key]" class="mb-2">
        <template #label>{{ value.label || value }}</template>
        <template #description v-if="value.description">{{ value.description }}</template>
      </c-checkbox>
    </div>
    </div>
  </div>
</template>

<script>
import CInput from '@/components/reusable/atoms/CInput/CInput.vue'
import CCheckbox from '@/components/reusable/atoms/CCheckbox/CCheckbox.vue'

export default {
  components: {
    CInput,
    CCheckbox,
  },

  props: {
    form: {
      type: Object,
      required: true,
      validator: (v) => !Object.values(v).filter((v) => !v.label)
    },
    formData: {
      type: Object,
      required: true,
    }
  },

  setup(props) {
    return {
      fields: Object.entries(props.form).reduce((a, [key, value]) => ({
        ...a,
        [key]: {
          ...value,
          type: value.type || 'text',
        }

      }), {})
    }
  }
}
</script>
