<template>
  <div v-if="formData">
    <div class="grid gap-y-4 py-2" v-if="!isReadonly">
      <!-- form -->
      <div
        v-for="([key, field], index) in fields"
        :key="`field-${index}`"
      >

      <c-input v-if="['text', 'password', 'number'].includes(field.type)" :type="field.type" :placeholder="field.placeholder" :mask="field.mask" v-model="formData[key]">
        {{ field.label }}
      </c-input>

      <!-- checkbox and boolean -->
      <div v-else-if="['checkbox', 'boolean'].includes(field.type)">
        <header class="text-sm py-2">{{ field.label }}</header>
        <div class="grid grid-cols-2 gap-1">
          <c-checkbox v-if="field.type === 'checkbox'" v-for="(value, vindex) in field.values" :key="`value-${vindex}`" v-model="formData[key]" :array="Array.isArray(field.values)" :value="value">
            <template #label>{{ value.label || value }}</template>
            <template #description>{{ value.description }}</template>
          </c-checkbox>

          <c-checkbox v-else-if="field.type === 'boolean'" v-model="formData[key]" :value="formData[key] === 'true'">
            <template #label>{{ field.label }}</template>
          </c-checkbox>
        </div>
      </div>
      </div>
    </div>

    <div class="grid gap-y-2 mt-4" v-if="!isReadonly">
      <div v-for="([childModule, field], index) in moduleFields" :key="`modulefield-${index}`">
        <c-search
          v-if="!isReadonly"
          v-model="formData[childModule]"

          :model="childModule" 
          :module="field.module" :module-name="field.label?.capitalize()"

          :builtin="field.expand === true"
          :field="getFirstField(field)"
          :label="Object.values(field.fields)[0]?.label"
          :array="field.array"
          :active-only="'active' in field.fields"
          >
        </c-search>
      </div>
    </div>

    <div v-if="isReadonly" class="grid gap-y-2 text-md">
      <div
        v-for="([, field], index) in allInOne"
        :key="`module-${index}`"
        class="grid grid-cols-2"
        >
        <strong>{{ field.label }}</strong>
        <div v-for="(value, index) in field.value" :key="`value-${index}`">
          {{ value || '-' }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineAsyncComponent, inject } from 'vue'
import { useStore } from 'vuex'
import { CInput, CCheckbox } from '@/components/reusable'

export default {
  components: {
    CInput,
    CCheckbox,
    CSearch: defineAsyncComponent(() => import('@/components/reusable/molecules/CSearch/CSearch.vue')),
  },

  props: {
    form: {
      type: Object,
      required: true,
      validator: (v) => !Object.values(v).some((v) => !v.label)
    },
    formData: {
      type: Object,
      required: true,
    },

    // HTML5 has a DOM property with the same name
    isReadonly: {
      type: Boolean,
      default: false,
    }
  },

  setup(props) {

    const store = useStore()
    const module = inject('module', '')

    const filterFields = (condition) => 
      Object.entries(props.form)
        .filter(([, field]) => !field.meta)
        .filter(([, field]) => condition(field))

    const getFirstField = (module) =>
      Object.keys(module.fields)[0]

    const getFirstValue = (f, module, field) => {
      const value = f[module]
      const first = getFirstField(field)
      return Array.isArray(value)
        ? value.map((v) => (v||{})[first])
        : [(value||{})[first]]
    }

    const joinIfArray = (v) =>
      Array.isArray(v)
        ? [v.join(', ')]
        : [v]

    const fields = filterFields((f) => typeof f.module !== 'string')
    const moduleFields = filterFields((f) => typeof f.module === 'string')
    .map(([key, value]) => [key, {
      array: !!value.array,
      expand: !!value.expand,
      label: value.label,
      ...store.getters[`${value.module}/description`]
    }])

    return {
      module,
      fields,
      moduleFields,
      allInOne: [ ...fields, ...moduleFields ]
      .sort((a, b) => typeof a.module === typeof b.module ? 1 : -1)
      .map(([key, field]) => {

        return [key, {
        label: field.label,
        ...(typeof field.module !== 'string'
          ? { value: joinIfArray((props.formData||{})[key]) } 
          : { value: joinIfArray(getFirstValue(props.formData, key, field)) })
      }]
      }),

      getFirstField,
      getFirstValue,
      joinIfArray,
    }
  }
}
</script>
