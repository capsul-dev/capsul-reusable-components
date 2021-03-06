<template>
  <div v-if="visible" :class="`${ isFloating ? 'absolute z-50' : 'mb-2' } ${ animate ? 'animate-fade' : '' }`" @click="$emit('close')">
    <div v-if="isFloating" class="fixed inset-0 bg-gray-900 opacity-60"></div>
    <div :class="`${ isFloating ? 'fixed inset-0 flex justify-center items-center' : ''}`">
      <div
        @click="$event.stopPropagation()"
        :class="`
          ${
            isFloating
              ? 'w-full h-full sm:w-3/4 md:w-3/5 lg:w-5/12 sm:h-auto sm:min-h-modal z-10 max-h-screen md:max-h-modal'
              : 'rounded-md shadow'
          }
          ${
            isFloating && animate
              ? 'animate-toast' : ''
          }
          sm:rounded-lg
          flex flex-col
          bg-white
          px-2 py-3 md:px-5 md:py-5
        `"
      >
        <div class="flex mb-6">
          <div v-if="$slots.title || title" class="flex-1 font-semibold text-lg">
            <slot v-if="$slots.title" name="title"></slot>
            <div v-else-if="title">{{ title }}</div>
          </div>
          <c-button :bare="true" v-if="closeHint || collapsable">
            <i v-if="collapsable" @click="isCollapsed = !isCollapsed">[A]</i>
            <i v-else-if="closeHint" @click="$emit('update:visible', false)" class="fa fa-close">Fechar</i>
          </c-button>
        </div>

        <div v-if="!isCollapsed" :class="`overflow-auto flex-grow ${$slots.footer ? 'border-b pb-5' : ''}`">
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
import { CButton } from '@/components/reusable'

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
    },
    animate: {
      type: Boolean,
      default: true,
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

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: all .15s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
