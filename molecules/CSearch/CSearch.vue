<template>
  <div class="border p-2">
    <header class="font-semibold mb-1">{{ moduleName }}</header>
    <div v-if="expand">
      <c-form :form="fields" :form-data="item">
      </c-form>
      <c-button :bare="true" class="justify-self-end mr-2">Salvar</c-button>
      <c-button :bare="true" @click="clear">Limpar</c-button>
    </div>

    <div v-else class="flex">
      <c-input class="flex-1 pr-4" @input="lazySearch" v-model="inputValue">{{ label }}</c-input>
      <c-button class="self-end" @click="expand = true">Adicionar</c-button>
    </div>

    <div v-if="!expand" :class="`grid gap-y-1 mt-4 select-none ${isLoading ? 'opacity-30' : ''}`">
      <div v-for="(item, index) in items" :key="`item-${index}`" @click="select(item)">
        <div class="cursor-pointer p-2 border">{{ item[field] }}</div>
      </div>
    </div>

    <div v-for="(item, index) in selected" :key="`item-${index}`">
      <div>{{ item[field] }}</div>
      <div @click="unselect(item)">Deletar</div>
    </div>
  </div>
</template>

<script>
import { computed, ref, defineAsyncComponent, onMounted } from 'vue'
import { useStore } from 'vuex'

import CInput from '@/components/reusable/atoms/CInput/CInput.vue'
import CButton from '@/components/reusable/atoms/CButton/CButton.vue'

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
    }
  },

  methods: {
    clear() {
      this.expand = false
      this.store.dispatch(`${this.module}/clear`)
    },

    select(item) {
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

    onMounted(() => store.dispatch(`${props.module}/clearAll`))

    return {
      store,
      expand: ref(false),
      fields: computed(() => store.getters[`${props.module}/fields`]),
      item: computed(() => store.state[props.module].item),
      items: computed(() => store.state[props.module].items),
      isLoading: computed(() => store.state[props.module].isLoading),

      inputValue: computed(() => (props.modelValue||{})[props.field]),
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
