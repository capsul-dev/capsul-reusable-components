<template>
  <div class="h-10 bg-red-100">
    <div class="bg-red-200">
      Capsul INC
    </div>
    <div class="grid grid-cols-2 justify-between w-full p-2">
      <div @click="$store.dispatch('meta/swapMenu')">Menu</div>
      <div class="ml-auto">
        Opções
      </div>
    </div>
  </div>
</template>
