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
          :type="action.type"

          @clicked="onClick(action)"
        >
          {{ action.title }}
        </c-button>
      </div>
    </template>
  </c-modal>
</template>

<script>
import { defineAsyncComponent } from 'vue'
import { useStore } from 'vuex'
import { CButton } from '@/components/reusable'

export default {
  components: {
    CModal: defineAsyncComponent(() => import('@/components/reusable/organisms/CModal/CModal.vue')),
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
