<template>
  <div class="grid gap-y-4">
    <table class="w-full table-fixed border sm:text-center" v-if="rows.length > 0">
      <tr class="border-b leading-8 bg-gray-100">
        <th class="hidden sm:table-cell border-r w-10" v-if="module">
          <input type="checkbox" @change="store.dispatch(`${module}/selectAll`, $event.target.checked)" />
        </th>
        <th
          v-for="(header, index) in columns"
          :key="`header-${index}`"
          class="hidden sm:table-cell truncate border-r px-1"
        >
          {{ header.title || header }}
        </th>
      </tr>

      <tr v-for="(row, rindex) in rows" :key="`row-${rindex}`" :class="`block mb-8 sm:table-row leading-7 ${rindex %2 !== 0 ? 'bg-gray-100' : ''}`">
        <td class="border-r hidden sm:table-cell" v-if="module">
          <input type="checkbox" v-model="selected" :value="row"/>
        </td>
        <td
          v-for="([column, label], cindex) in Object.entries(columns)"
          :key="`column-${rindex}-${cindex}`"
          class="block sm:table-cell truncate border-r text-md px-1"
        >
        <div class="grid grid-cols-2 md:inline-block justify-between">
          <div class="font-semibold sm:hidden text-ellipsis truncate">{{ label.title || label }}</div>

          <!-- status cell -->
          <c-status
            v-if="column === 'status'"
            :status="row[column]"
            :key="`status-${row._id || rindex}`"
            >
          </c-status>

          <div v-else-if="column !== '__custom'" class="opacity-80">
            {{ cell(row, column, columns) }}
          </div>

          <div v-else class="flex gap-x-1">
            <c-button :bare="true" v-for="(action, aindex) in columns.__custom.actions" :key="`action-${rindex}-${aindex}`" @clicked="action.click(row)" class="cursor-pointer">
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
import { inject } from 'vue'
import { useStore } from 'vuex'
import { CButton, CStatus } from '@/components/reusable'

export default {
  components: {
    CButton,
    CStatus,
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

        const field = columns[column].index
          ? columns[column].index
          : Object.keys(this.store.getters[`${columns[column].module}/tableDescription`])[0]

        const formatCell = v => v[field] || '-'

        return Array.isArray(row[column])
          ? row[column].reduce((a, c) => a + `${a.length > 0 ? ', ' : ''}${formatCell(c)}`, '')
          : formatCell(row[column])
      }

      if( Array.isArray(row[column]) ) {
        return row[column].join(', ') 
      }

      return columns[column].type === 'datetime'
        ? row[column].formatDateTime()
        : row[column]
    },
  },

  computed: {
    selected: {
      get() {
        return this.store.state[this.module].selected
      },
      set(items) {
        this.store.dispatch(`${this.module}/selectMany`, { items, value: true })
      }
    }
  },

  setup() {
    const store = useStore()
    const module = inject('module')

    return {
      store,
      module,
    }
  }
}
</script>
