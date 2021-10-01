<template>
  <div class="relative bg-white rounded-lg shadow-lg mb-4 p-3">
    <c-button
      :bare="true"
      class="absolute top-1 right-2"
      @click="collapsed = !collapsed"
    >
      [A]
    </c-button>
    <slot v-if="!collapsed"></slot>
    <div v-else>{{ props.title }}</div>
  </div>
</template>

<script>
import { ref } from 'vue'
import CButton from '@/components/reusable/atoms/CButton/CButton.vue'

export default {
  props: {
    title: {
      type: String,
      required: true,
    }
  },

  components: {
    CButton,
  },

  setup(props) {
    return {
      collapsed: ref(false),
      props,
    }
  }
}
</script>
