<template>
  <div v-if="visible" :class="`${ isFloating ? 'absolute z-10' : 'mb-3 md:mb-4' }`" @click="closeModal">
    <div v-if="isFloating" class="fixed inset-0 bg-gray-900 opacity-50"></div>
    <div :class="`${ isFloating ? 'fixed inset-0 flex justify-center items-center' : ''}`">
      <div
        @click="$event.stopPropagation()"
        :class="`
          ${
            isFloating
              ? 'w-full h-full sm:w-3/4 md:w-3/5 lg:w-5/12 sm:h-auto sm:min-h-modal z-10'
              : 'rounded-md shadow-md'
          }
          sm:rounded-lg sm:shadow-lg
          flex flex-col
          bg-white
          p-3 md:p-5 max-h-screen md:max-h-modal
        `"
      >
        <div class="flex mb-6">
          <div v-if="$slots.title || title" class="flex-1 font-bold text-xl">
            <slot v-if="$slots.title" name="title"></slot>
            <div v-else-if="title">{{ title }}</div>
          </div>
          <c-button :bare="true" v-if="closeHint || collapsable">
            <i v-if="collapsable" @click="isCollapsed = !isCollapsed">[A]</i>
            <i v-else-if="closeHint" @click="$emit('update:visible', false)" class="fa fa-close">Fechar</i>
          </c-button>
        </div>

        <div v-if="!isCollapsed" :class="`overflow-y-scroll flex-grow ${$slots.footer ? 'border-b pb-5' : ''}`">
          <slot name="body"></slot>
        </div>

        <div class="self-end mt-2" v-if="$slots.footer">
          <slot name="footer"></slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { computed, ref } from 'vue'
import CButton from '@/components/reusable/atoms/CButton/CButton.vue'

export default {
  props: {
    title: {
      type: String,
      required: false
    },
    closeHint: {
      type: Boolean,
      default: true,
    },
    float: {
      type: Boolean,
      default: false,
    },
    floating: {
      type: Boolean,
      default: false
    },
    collapsed: {
      type: Boolean,
      default: false,
    },
    visible: {
      type: Boolean,
      default: true,
    },
    collapsable: {
      type: Boolean,
      default: false,
    }
  },

  components: {
    CButton,
  },

  setup(props) {
    return {
      isFloating: computed(() => props.floating || props.float),
      isCollapsed: ref(props.collapsed)
    }
  },
}
</script>
