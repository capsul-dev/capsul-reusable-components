<template>
  <div :class="`
  sticky z-10 w-screen h-screen md:relative md:w-auto md:h-auto bg-gray-100 shadow-lg
  ${
    visible
      ? 'hidden md:block'
      : 'block md:hidden'
  } 
    `">

    <div class="h-52 mb-6 border">
      Usuário
    </div>

    <!-- menu entries -->
    <div class="grid py-2">
      <div
        v-for="(route, index) in filterRoutes(routes)"
        :key="`route-${index}`"
        class="border-b border-indigo-200 py-2"
      >
        <router-link 
          :to="{ name: route.name }"
        >
        <div class="pl-2">
          {{ route.meta.title }}
        </div>
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
    }
  },

  setup(props) {
    const store = useStore()
    const router = useRouter()

    const getRoutes = () => {
      return typeof props.entrypoint === 'string'
        ? router.getRoutes().filter((route) => route.name.startsWith(`${props.entrypoint}-`))
        : router.getRoutes()
    }

    const routes = ref(getRoutes())
    const filterRoutes = (routes) => (routes || []).filter((route) => route.meta && !route.meta.hidden)

    watch(() => store.state.meta?.globalDescriptions, () => {
      routes.value = getRoutes()
        .sort((a, b) => (a.meta.order||0) < (b.meta.order||0) ? -1 : 1)
    })

    return {
      tick: ref(0),
      routes,
      getRoutes,
      filterRoutes,
    }
  },
}
</script>
