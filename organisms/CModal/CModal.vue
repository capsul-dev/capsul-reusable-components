<template>
  <c-box :float="true" :close-hint="true">
    <template #title>
      <slot name="title"></slot>
    </template>
    <template #body>
      <slot name="body"></slot>
    </template>
    <template #footer>
      <slot name="footer"></slot>
    </template>
  </c-box>
</template>

<script>
import CBox from '@/components/reusable/molecules/CBox/CBox.vue'

export default {
  components: {
    CBox
  }
}
</script>
