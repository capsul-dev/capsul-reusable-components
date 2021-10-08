<template>
  <div class="grid gap-y-4">
    <table class="text-left w-full max-w-full table-auto" v-if="rows.length > 0">
      <tr class="border-b leading-8">
        <th
          v-for="(header, index) in columns"
          :key="`header-${index}`"
          class="hidden sm:table-cell"
        >
          {{ header.title || header }}
        </th>
      </tr>

      <tr v-for="(row, rindex) in rows" :key="`row-${rindex}`" :class="`block mb-8 sm:table-row leading-7 ${rindex %2 !== 0 ? 'bg-gray-100' : ''}`">
        <td
          v-for="([column, label], cindex) in Object.entries(columns)"
          :key="`column-${rindex}-${cindex}`"

          class="block sm:table-cell"
        >
        <div class="grid grid-cols-2 md:inline-block justify-between">
          <div class="font-semibold sm:hidden text-ellipsis truncate">{{ label.title || label }}</div>
          <div v-if="column !== '__custom'" class="opacity-80">
            {{ cell(row, column, columns) }}
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

    <div v-if="recordsCount && recordsTotal" class="justify-self-end text-sm opacity-80">
      Mostrando {{ recordsCount }} de {{ recordsTotal }} registros
    </div>
  </div>
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
    },
    recordsCount: {
      type: Number,
      required: false,
    },
    recordsTotal: {
      type: Number,
      required: false,
    }
  },

  methods: {
    cell(row, column, columns) {
      if( String(row[column]||'').length === 0 ) return '-'
      if( columns[column].type === 'module' ) {
        const formatCell = v => Object.values(v||{})[1] || ''
        return Array.isArray(row[column])
          ? row[column].reduce((a, c) => a + `${a.length > 0 ? ', ' : ''}${formatCell(c)}`, '')
          : formatCell(row[column])
      }

      return columns[column].type === 'datetime'
        ? row[column].formatDateTime()
        : row[column]
    }
  }
}
</script>
