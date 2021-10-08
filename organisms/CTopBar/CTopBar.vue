<template>
  <div class="h-10 bg-red-100 z-20">
    <div class="bg-red-200">
      Capsul INC
    </div>
    <div class="grid grid-cols-2 justify-between w-full p-2">
      <c-button :bare="true" @click="$store.dispatch('meta/swapMenu')">Menu</c-button>
      <div class="ml-auto">
        Opções
      </div>
    </div>
  </div>
</template>

<script>
import CButton from '@/components/reusable/atoms/CButton/CButton.vue'

export default {
  components: {
    CButton
  }
}
</script>
