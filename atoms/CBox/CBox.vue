<template>
  <div class="relative bg-white rounded-lg shadow-lg mb-4 p-3">
    <div
      class="absolute top-1 right-2 cursor-pointer"
      @click="collapsed = !collapsed"
    >
      [A]
    </div>
    <slot v-if="!collapsed"></slot>
    <div v-else>{{ props.title }}</div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  props: {
    title: {
      type: String,
      required: true,
    }
  },

  setup(props) {
    return {
      collapsed: ref(false),
      props,
    }
  }
}
</script>
