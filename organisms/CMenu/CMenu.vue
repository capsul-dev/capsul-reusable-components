<template>
  <div :class="`
  fixed z-10 w-screen h-screen md:relative md:w-auto md:h-auto bg-indigo-500 text-gray-50
  ${
    visible
      ? 'hidden md:block'
      : 'block md:hidden'
  } 
    `">

    <!-- branding -->
    <div class="h-52 mb-6 border border-red-600">
      Usuário
    </div>

    <!-- menu entries -->
    <div class="grid p-2">
      <div
        v-for="(route, index) in filterRoutes(routes)"
        :key="`route-${index}`"
        class="border-b border-indigo-200 mb-3"
      >
        <router-link 
          :to="{ name: route.name }"
        >
          {{ route.meta.title }}
        </router-link>

        <!-- subroutes -->
        <div
          v-for="(subroute, index) in filterRoutes(route.children)"
          :key="`subroute-${index}`"
        >
          <router-link :to="{ name: subroute.name }">{{ subroute.meta.title }}</router-link>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
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
    }
  },

  setup(props) {
    const router = useRouter()
    const routes = typeof props.entrypoint === 'string'
      ? router.getRoutes().find((route) => route.name === props.entrypoint).children
      : router.getRoutes()

    const filterRoutes = (routes) => (routes || []).filter((route) => route.meta && !route.meta.hidden)

    return {
      routes,
      filterRoutes,
    }
  },
}
</script>
