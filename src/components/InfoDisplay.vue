<script setup>
import { computed } from 'vue'
import FlowFieldCanvas from './FlowFieldCanvas.vue';
import WatercolorCanvas from './WatercolorCanvas.vue';
import MondrianGridCanvas from './MondrianGridCanvas.vue';
import MapTraceCanvas from './MapTraceCanvas.vue';
import HedgehogCanvas from './HedgehogCanvas.vue';
import PatchworkCanvas from './PatchworkCanvas.vue';
import FractalTreeCanvas from './FractalTreeCanvas.vue';
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
  '/mondrian': MondrianGridCanvas,
  '/maptrace': MapTraceCanvas,
  '/hedgehog': HedgehogCanvas,
  '/patchwork': PatchworkCanvas,
  '/fractal': FractalTreeCanvas
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
