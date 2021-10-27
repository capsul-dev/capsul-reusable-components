<template>
  <div class="flex sticky inset-0 md:grid md:grid-cols-menu h-10 z-40 text-white">
    <div class="bg-blue-800 cursor-pointer" @click="$router.push({ name: 'dashboard-home' })">
      Kampfsul INC
    </div>
    <div class="grid grid-cols-2 justify-between w-full p-2 bg-gradient-to-r from-blue-600 via-indigo-600 to-blue-700">
      <c-button :bare="true" @clicked="$store.dispatch('meta/swapMenu')">Menu</c-button>
      <div class="ml-auto">
        Opções
      </div>
    </div>
  </div>
</template>

<script>
import { CButton } from '@/components/reusable'

export default {
  components: {
    CButton
  },
}
</script>
