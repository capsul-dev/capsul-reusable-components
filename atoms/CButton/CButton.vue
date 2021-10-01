<template>
  <a
    :class="`
      ${
        !!props.isLoading
          ? 'bg-gray-200'
          : 'bg-gradient-to-r from-green-500 to-green-700'
      }
      text-white text-center
      font-bold
      py-2
      px-4
      rounded
      cursor-pointer
      inline-block select-none
      `"

    @click="onClick"
  >
    <slot></slot>
  </a>
</template>

<script>
export default {
  props: {
    isLoading: {
      type: Boolean,
      default: false,
    },
  },

  methods: {
    onClick(event) {
      event.stopPropagation()
      event.preventDefault()

      if( !this.isLoading ) {
        this.$emit('clicked', event)
      }
    }
  },

  setup(props) {
    return {
      props,
    };
  },
};
</script>
