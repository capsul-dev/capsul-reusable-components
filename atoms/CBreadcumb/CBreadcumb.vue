<template>
  <div class="pt-2 pl-2 text-xs">
    <router-link class="_link text-md" v-for="(route, index) in routes" :key="`route-${index}`" :to="{ name: route.name, params: $route.params }">
      {{ route.meta.title === '%viewTitle%' ? viewTitle : route.meta.title }}
    </router-link>
  </div>
</template>

<script>
import { computed } from 'vue'
import { useStore } from 'vuex'
import { useRoute } from 'vue-router'

export default {
  setup() {

    const store = useStore()
    const getRoute = () => {
      const route = useRoute()
      return route.matched || [route]
    }

    return {
      viewTitle: computed(() => store.state.meta.viewTitle),
      routes: computed(getRoute)
    }
  }
}
</script>

<style>
._link:not(:last-child):after {
  content: '»';
  margin: 0 5px;
  vertical-align: center;
}
</style>
