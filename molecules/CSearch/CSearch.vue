<template>
  <div class="border p-2">
    <header class="font-semibold mb-1">{{ moduleName }}</header>
    <div v-if="isExpanded">
      <c-form :form="fields" :form-data="item">
      </c-form>
      <c-button :bare="true" class="justify-self-end mr-2">Salvar</c-button>
      <c-button :bare="true" @clicked="clear" v-if="!builtin">Limpar</c-button>
    </div>

    <div v-else class="flex">
      <c-input class="flex-1 pr-4" @input="lazySearch" v-model="inputValue">{{ label }}</c-input>
      <c-button :bare="true" class="self-end" @clicked="expanded = true">Adicionar</c-button>
    </div>

    <div v-if="!isExpanded">
      <div :class="`grid select-none ${isLoading ? 'opacity-30' : ''}`">
        <div v-for="(item, index) in items" :key="`item-${index}`" @click="select(item)">
          <div class="cursor-pointer p-2 border">{{ item[field] }}</div>
        </div>
      </div>

        <div v-if="selected.length > 0" class="mt-4">
        <div v-for="(item, index) in selected" :key="`item-${index}`" class="flex justify-between p-2 border">
          <div>{{ item[field] }}</div>
          <c-button :bare="true" @clicked="unselect(item)">Deletar</c-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { inject, computed, ref, defineAsyncComponent, onMounted } from 'vue'
import { useStore } from 'vuex'
import { CInput, CButton } from '@/components/reusable'

export default {
  components: {
    CInput,
    CButton,
    CForm: defineAsyncComponent(() => import('@/components/reusable/molecules/CForm/CForm.vue')),
  },

  props: {
    modelValue: {
      type: Object,
      required: true,
    },
    model: {
      type: String,
      required: true,
    },
    module: {
      type: String,
      required: true,
    },
    moduleName: {
      type: String,
      required: true,
    },
    field: {
      type: String,
      required: true,
    },
    label: {
      type: String,
      required: true,
    },
    array: {
      type: Boolean,
      required: true,
    },
    builtin: {
      type: Boolean,
      default: false,
    },
    activeOnly: {
      type: Boolean,
      default: false,
    }
  },

  methods: {
    clear() {
      this.expanded = false
      this.store.dispatch(`${this.module}/clear`)
    },

    select(item) {
      this.inputValue = ''
      this.store.dispatch(`${this.module}/clearAll`)
      this.$emit('update:modelValue', this.array
        ? [ ...this.modelValue, item ]
        : item
      )
    },

    unselect(item) {
      this.$emit('update:modelValue', this.array
          ? this.modelValue.filter((option) => option._id !== item._id)
          : undefined
      )
    },

    search(value) {
      if( value.length === 0 ) {
        this.store.dispatch(`${this.module}/clearAll`)
        return
      }

      if( this.store.state[this.module].isLoading ) {
        return
      }

      this.store.dispatch(`${this.module}/getAll`, {
        filter: {
          ...(this.activeOnly ? { active: 'true' } : {}),
          [this.field]: {
            $regex: value.trim(),
            $options: 'i'
          }
        }
      })
    },

    lazySearch({ target: { value } }) {
      window.clearTimeout(window.__lazySearchTimeout)
      window.__lazySearchTimeout = setTimeout(() => {
        this.search(value)
      }, 800)
    }
  },

  setup(props) {
    const store = useStore()
    const module = inject('module')
    const expanded = ref(false)

    onMounted(() => store.dispatch(`${props.module}/clearAll`))

    return {
      store,
      expanded,
      isExpanded: computed(() => expanded.value || props.builtin),
      fields: computed(() => store.getters[`${props.module}/fields`]),
      item: computed(() => store.state[module._value].item[props.model]),
      items: computed(() => store.state[props.module].items),
      isLoading: computed(() => store.state[props.module].isLoading),

      inputValue: ref(''),
      selected: computed(() => {
        const options = props.modelValue
        return props.array
          ? options
          : (Object.keys(options||{}).length > 0 ? [options] : [])
      })
    }
  }
}
</script>
