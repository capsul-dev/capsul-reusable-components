<template>
  <a
    :class="`
      inline-block select-none
      ${
        !props.bare ? (props.isLoading
          ? 'bg-gray-200'
          : 'bg-gradient-to-r from-green-500 to-green-700') : ''
      }
      ${
        !props.bare
          ? 'text-white text-center font-bold py-2 px-4 rounded'
          : ''
      }
      ${
        !props.isLoading
          ? 'cursor-pointer'
          : 'cursor-not-allowed'
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
    isLoading: {
      type: Boolean,
      default: false,
    },
    bare: {
      type: Boolean,
      default: false,
    }
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
