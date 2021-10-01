<template>
  <table class="text-left w-full max-w-full">
    <tr class="border-b leading-8">
      <th
        v-for="(header, index) in columns"
        :key="`header-${index}`"
        class="hidden sm:table-cell"
      >
        {{ header.title || header }}
      </th>
    </tr>

    <tr v-for="(row, rindex) in rows" :key="`row-${rindex}`" class="block mb-8 sm:table-row sm:border-b leading-7">
      <td
        v-for="([column, label], cindex) in Object.entries(columns)"
        :key="`column-${rindex}-${cindex}`"

        class="block sm:table-cell border-b"
      >
      <div class="grid grid-cols-2 md:inline-block justify-between">
        <div class="font-semibold sm:hidden">{{ label.title || label }}</div>
        <div v-if="column !== '__custom'">
          {{ row[column] }}
        </div>

        <div v-else class="flex gap-x-2">
          <c-button :bare="true" v-for="(action, aindex) in columns.__custom.actions" :key="`action-${rindex}-${aindex}`" @click="action.click(row)" class="cursor-pointer">
            {{ action.name }}
          </c-button>
        </div>
      </div>
      </td>
    </tr>
  </table>
</template>

<script>
import CButton from "@/components/reusable/atoms/CButton/CButton.vue"

export default {
  components: {
    CButton,
  },

  props: {
    columns: {
      type: Object,
      required: true,
    },
    rows: {
      type: Object,
      required: true,
      validator: (v) => Array.isArray(v)
    }
  }
}
</script>
