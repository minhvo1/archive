<script setup>
import { ref, onMounted } from 'vue'

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

onMounted(() => {
  let parts = document.URL.split('/')
  let target = document.getElementById(parts[parts.length - 1])
  if (target) {
    target.scrollIntoView()
  }
})

</script>

<template>
  <div class="side-nav pl-3 shadow-inner">
    <div class="header mb-9">
      <a href="#/">ARCHIVE</a>
    </div>

    <div class="menu text-6xl mt-16">
      <div id="flowfield" class="menu-item">
        <a href="#/flowfield">FLOW FIELD</a>
      </div>
      <div id="watercolor" class="menu-item">
        <a href="#/watercolor">WATERCOLOR</a>
      </div>
      <div id="mondrian" class="menu-item">
        <a href="#/mondrian">MONDRIAN GRID</a>
      </div>
      <div id="maptrace" class="menu-item">
        <a href="#/maptrace">MAP TRACE</a>
      </div>
      <div id="fractal" class="menu-item">
        <a href="#/fractal">FRACTAL TREE</a>
      </div>
      <div id="hedgehog" class="menu-item">
        <a href="#/hedgehog">HEDGEHOG</a>
      </div>
      <div id="patchwork" class="menu-item">
        <a href="#/patchwork">PATCHWORK</a>
      </div>
   
    </div>
  </div>
</template>

<style lang="scss">
@import '../styles/sideNav.scss';
</style>