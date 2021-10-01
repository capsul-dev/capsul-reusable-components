<template>
  <div
    class="
    checkbox
    rounded
    bg-white
    shadow-md
    py-2
    grid grid-cols-checkbox
    items-center
    select-none
    "
    >
    <div class="justify-self-center">
      <input
        ref="checkbox"
        type="checkbox"
        :checked="modelValue"
        :disabled="!!required"
        @input="onInput"
        />
    </div>
    <div
      class="grid border-l-2 px-4 cursor-pointer"
      @click="onClick"
      >
      <div class="font-semibold">
        <slot name="label" v-if="$slots.label"></slot>
      </div>
      <div class="opacity-80">
        <slot name="description" v-if="$slots.description"></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    modelValue: {
      type: Boolean,
      required: true,
    },
    required: {
      type: Boolean,
      default: false,
    },
  },

  methods: {
    onInput(event) {
      this.$emit("update:modelValue", event.target.checked);
      this.$emit("valueChanged");
    },
    onClick() {
      if (!this.required) {
        this.$refs.checkbox.click();
      }
    },
  },
};
</script>
