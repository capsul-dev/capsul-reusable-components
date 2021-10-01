<template>
  <div class="grid gap-y-4 py-2 px-2">
    <!-- form -->
    <div
      v-for="([key, field], index) in Object.entries(fields)"
      :key="`field-${index}`"
      class="w-80"
    >

    <c-input v-if="['text', 'password', 'number'].includes(field.type)" :type="field.type" :placeholder="field.placeholder" v-model="formData[key]">
      {{ field.label }}
    </c-input>

    <c-checkbox v-else-if="field.type === 'checkbox'" v-model="formData[key]">
      <template #label>{{ field.label }}</template>
      <template #description v-if="field.description">{{ field.description }}</template>
    </c-checkbox>
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
          type: value.type || 'text'
        }

      }), {})
    }
  }
}
</script>
