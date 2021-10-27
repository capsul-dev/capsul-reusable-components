<template>
  <c-box :float="true" :close-hint="true">
    <template #title>
      <slot name="title"></slot>
    </template>
    <template #body>
      <slot name="body"></slot>
    </template>
    <template #footer v-if="$slots.footer">
      <slot name="footer"></slot>
    </template>
  </c-box>
</template>

<script>
import { CBox } from '@/components/reusable'

export default {
  components: {
    CBox
  }
}
</script>
