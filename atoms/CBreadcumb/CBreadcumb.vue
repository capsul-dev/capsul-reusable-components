<template>
  <div class="border border-red-900 pl-2">
    <router-link class="_link" v-for="(route, index) in routes" :key="`route-${index}`" :to="{ name: route.name }">
      {{ route.meta.title }}
    </router-link>
  </div>
</template>

<script>
import { computed } from 'vue'
import { useRoute } from 'vue-router'

export default {
  setup() {

    const getRoute = () => {
      const route = useRoute()
      return route.matched || [route]
    }

    return {
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
