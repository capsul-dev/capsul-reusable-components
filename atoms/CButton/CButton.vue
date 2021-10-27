<template>
  <a
    :class="`
      inline-block select-none
      ${
        !bare
          ? `text-white text-center font-bold py-2 px-4 rounded bg-${type}-500 border-b-4 border-${type}-700 filter drop-shadow-lg`
          : ''
      }
      ${
        !disabled
          ? 'cursor-pointer'
          : 'cursor-not-allowed opacity-50'
      }
      `"

    @click="onClick"
  >
    <slot></slot>
  </a>
</template>

<script>
export default {
  props: {
    disabled: {
      type: Boolean,
      default: false,
    },
    bare: {
      type: Boolean,
      default: false,
    },
    type: {
      type: String,
      default: 'success',
    }
  },

  methods: {
    onClick(event) {
      event.stopPropagation()
      event.preventDefault()

      if( !this.disabled ) {
        this.$emit('clicked', event)
      }
    }
  },
};
</script>
