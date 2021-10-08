<template>
  <div class="grid gap-y-4 py-2 px-2">
    <!-- form -->
    <div
      v-for="([key, field], index) in fields"
      :key="`field-${index}`"
    >


    <c-input v-if="['text', 'password', 'number'].includes(field.type)" :type="field.type" :placeholder="field.placeholder" :mask="field.mask" v-model="formData[key]">
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
  <div class="grid gap-y-2 mt-4">
    <div v-for="([module, field], index) in moduleFields" :key="`modulefield-${index}`">
      <c-search
        v-model="formData[module]"
        :model="module" :module="field.module" :module-name="field.name.capitalize()"
        :field="Object.keys(field.fields)[0]"
        :label="Object.values(field.fields)[0]?.label"
        :array="field.array"
        @add="$emit('add', $event)">
      </c-search>
    </div>
  </div>
</template>

<script>
import { useStore } from 'vuex'

import CInput from '@/components/reusable/atoms/CInput/CInput.vue'
import CCheckbox from '@/components/reusable/atoms/CCheckbox/CCheckbox.vue'
import CSearch from '@/components/reusable/molecules/CSearch/CSearch.vue'

export default {
  components: {
    CInput,
    CCheckbox,
    CSearch,
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

    const store = useStore()

    const filterFields = (condition) => 
      Object.entries(props.form)
        .filter(([, field]) => !field.meta)
        .filter(([, field]) => condition(field))

    return {
      fields: filterFields((f) => typeof f.module !== 'string'),
      moduleFields: filterFields((f) => typeof f.module === 'string')
      .map(([key, value]) => [key, {
        array: !!value.array,
        ...store.getters[`${value.module}/description`]
      }])
    }
  }
}
</script>
