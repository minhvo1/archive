<script setup>
// import { computed } from 'vue'
import FlowFieldCanvas from './FlowFieldCanvas.vue';
// import WatercolorCanvas from './WatercolorCanvas.vue';
// import Home from './Home.vue';

// const routes = {
//   '/': Home,
//   '/flowfield': FlowFieldCanvas,
//   '/watercolor': WatercolorCanvas
// }

// const currentView = computed(() => {
//   return routes[currentPath.value.slice(1) || '/']
// })
</script>

<template>
  <div class="display flex-auto">
    <FlowFieldCanvas/>

  </div>
</template>

<style lang="scss">
  @import '../styles/infoDisplay.scss';
</style>
