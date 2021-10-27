<template>
  <div class="grid">
    <div class="flex justify-self-end text-sm">
      <c-button :bare="true" @clicked="paginate('-1')">Prev</c-button>
      <c-button :bare="true" @clicked="paginate('+1')" class="order-last">Next</c-button>
      <c-button
        v-for="page in pageCount"
        :key="`page-${page}`"
        :bare="true"
        :class="`border px-2 ${currentPage === page ? 'bg-grey-200' : ''}`"
        @clicked="paginate(page)"
      >
        {{ page }}
      </c-button>
    </div>
  </div>
</template>

<script>
import { computed, defineAsyncComponent } from 'vue'
import { useStore } from 'vuex'

export default {
  props: {
    module: {
      type: String,
      required: true,
    }
  },

  components: {
    CButton: defineAsyncComponent(() => import('@/components/reusable/atoms/CButton/CButton.vue'))
  },

  methods: {
    paginate(page) {
      this.store.dispatch(`${this.module}/paginate`, page)
    }
  },

  setup(props) {
    const store = useStore()
    return {
      store,
      pageCount: computed(() => store.getters[`${props.module}/pageCount`]),
      currentPage: computed(() => store.getters[`${props.module}/currentPage`]),
    }
  }
}
</script>
