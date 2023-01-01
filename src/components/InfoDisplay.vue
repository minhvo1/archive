<script setup>
import { computed } from 'vue'
import FlowFieldCanvas from './FlowFieldCanvas.vue';
import WatercolorCanvas from './WatercolorCanvas.vue';
import MondrianGridCanvas from './MondrianGridCanvas.vue'
import Home from './HomePage.vue';
import { ref } from 'vue'

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const routes = {
  '/': Home,
  '/flowfield': FlowFieldCanvas,
  '/watercolor': WatercolorCanvas,
  '/mondrian': MondrianGridCanvas
}

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/']
})

</script>

<template>
  <div id="display-wrapper" class="display flex-auto">
    <component :is="currentView" />
  </div>
</template>

<style lang="scss">
  @import '../styles/infoDisplay.scss';
</style>
