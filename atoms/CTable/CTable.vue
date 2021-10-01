<template>
  <table class="text-left w-full">
    <tr class="border-b leading-8">
      <th
        v-for="(header, index) in columns"
        :key="`header-${index}`"
      >
        {{ header.title || header }}
      </th>
    </tr>

    <tr v-for="(row, rindex) in rows" :key="`row-${rindex}`" class="border-b leading-7">
      <td
        v-for="(column, cindex) in Object.keys(columns)"
        :key="`column-${rindex}-${cindex}`"
      >
        <div v-if="column !== '__custom'">
          {{ row[column] }}
        </div>

        <div v-else class="flex gap-x-2">
          <a v-for="(action, aindex) in columns.__custom.actions" :key="`action-${rindex}-${aindex}`" @click="action.click(row)" class="cursor-pointer">
            {{ action.name }}
          </a>
        </div>
      </td>
    </tr>
  </table>
</template>

<script>
export default {
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
