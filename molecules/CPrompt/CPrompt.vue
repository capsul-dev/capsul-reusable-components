<template>
  <c-modal :close-hint="false">
    <template #title>
      <slot name="title"></slot>
    </template>

    <template #body>
      <slot name="body"></slot>
    </template>

    <template #footer>
      <div class="flex gap-x-2">
        <c-button
          v-for="(action, index) in actions"
          :key="`action-${index}`"

          @clicked="onClick(action)"
        >
          {{ action.title }}
        </c-button>
      </div>
    </template>
  </c-modal>
</template>

<script>
import { useStore } from 'vuex'

import CModal from '@/components/reusable/organisms/CModal/CModal.vue'
import CButton from '@/components/reusable/atoms/CButton/CButton.vue'

export default {
  components: {
    CModal,
    CButton,
  },

  props: {
    actions: {
      type: Array,
      required: true,
    }
  },

  methods: {
    onClick(action) {
      this.store.dispatch('meta/fulfillPrompt', action.name)
    }
  },

  setup() {
    const store = useStore()
    return {
      store
    }
  }
}
</script>
