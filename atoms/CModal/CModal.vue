<template>
  <div class="absolute z-10" @click="closeModal">
    <div class="fixed inset-0 bg-gray-900 opacity-50"></div>
    <div class="fixed inset-0 flex justify-center items-center">
      <div
        @click="$event.stopPropagation()"
        class="
          bg-white
          w-full
          h-full
          sm:w-1/2 sm:h-auto sm:min-h-modal
          z-10
          rounded-lg
          shadow-lg
          p-5

          flex flex-col
        "
      >
        <div class="flex mb-6">
          <div class="flex-1 font-bold text-2xl">
            <slot name="title"></slot>
          </div>
          <div class="cursor-pointer text-2xl opacity-50" @click="$store.dispatch('meta/closeModal')" v-if="closeHint">
            <a class="fa fa-close">Fechar</a>
          </div>
        </div>

        <div :class="`flex-grow ${$slots.footer ? 'border-b mb-2' : ''}`">
          <slot name="body"></slot>
        </div>

        <div class="self-end" v-if="$slots.footer">
          <slot name="footer"></slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    closeHint: {
      type: Boolean,
      default: true,
    }
  }
}
</script>
