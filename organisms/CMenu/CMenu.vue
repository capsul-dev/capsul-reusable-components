<template>
  <div :class="`
  fixed z-30 w-screen h-screen md:relative md:w-auto md:h-auto bg-gray-100 shadow-lg
  animate-slip md:animate-slowfade transition-all
  ${
    visible
      ? ''
      : 'block md:hidden'
  } 
  ${
    mobileVisible
      ? '' : 'hidden md:block'
  }
    `">
    <!-- top-10 is an hardcoded value -->
    <div class="sticky top-10">
      <div class="h-48 mb-6 border">
        Usuário
      </div>

      <!-- menu entries -->
      <div class="grid py-2 pl-2 text-xl leading-10 md:text-md md:leading-7">
        <div
          v-for="(route, index) in routes"
          :key="`route-${index}`"
          class="border-b py-2"
        >

          <div class="font-semibold" @click="closeMobile">
            <router-link :to="{ name: route.name }" v-if="route.name">
              {{ route.meta.title }}
            </router-link>
            <div v-else @click="route.meta.action">
              {{ route.meta.title }}
            </div>
          </div>

          <!-- subroutes -->
          <div
            v-for="(subroute, index) in route.children"
            :key="`subroute-${index}`"
          >
            <div class="opacity-80" @click="closeMobile">
              <router-link :to="{name: subroute.name }" v-if="subroute.name">
                {{ subroute.meta.title }}
              </router-link>
              <div v-else @click="subroute.meta.action">
                {{ subroute.meta.title }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import { ref, watch } from 'vue'
import { useStore } from 'vuex'
import { useRouter } from 'vue-router'

export default {
  props: {
    entrypoint: {
      type: String,
      required: false,
    },
    visible: {
      type: Boolean,
      required: true,
    },
    mobileVisible: {
      type: Boolean,
      required: true,
    },
    schema: {
      type: Object,
      required: false,
    }
  },

  methods: {
    closeMobile() {
      this.store.dispatch('meta/swapMenu', { desktop: true, mobile: false })
    }
  },

  setup(props) {
    const store = useStore()
    const router = useRouter()

    const getSchema = (schema, routes) => {
      if( !Array.isArray(schema) ) {
        return schema
      }

      return schema.map((s) => {
        return typeof s === 'string'
          ? routes.find((route) => route.name === s)
          : s
      })
    }

    const getRoutes = (children, subschema) => {
      const routes = children || typeof props.entrypoint === 'string'
        ? router.getRoutes().filter((route) => route.name.startsWith(`${props.entrypoint}-`))
        : router.getRoutes()

      const schema = getSchema(subschema || props.schema, routes)
      const entries = {}

      Object.entries(schema)
        .map(([key, value]) => [key, { ...value, subschema: value.children }])
        .forEach(([key, value]) => {
          const { children, subschema, ...route } = value
          entries[key] = route
          entries[key].meta = route.meta || {
            title: key
          }

          if( children ) {
            entries[key].children = getRoutes(children, subschema)
          }
        })

      return [
        ...Object.values(entries)
      ]

//      const entries = Object.entries(routes)
//        .filter(([, route]) => route.meta && !route.meta.hidden)
//        .reduce((a, [key, route]) => {
//          if( route.name in schema ) {
//            schema[route.name] = route
//            return a
//          }
//
//          return {
//            ...a,
//            [key]: {
//              title: route.meta?.title,
//              name: route.name,
//              children: route.children.map((c) => getRoutes(c)),
//              ...route.meta,
//            }
//          }
//        }, {})
    }

    const routes = ref(getRoutes())

    watch(() => store.state.meta?.globalDescriptions, () => {
      routes.value = getRoutes()
        .sort((a, b) => (a.order||0) < (b.order||0) ? -1 : 1)
    })

    return {
      store,
      tick: ref(0),
      routes,
    }
  },
}
</script>
