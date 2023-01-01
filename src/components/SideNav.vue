<script setup>
import { ref } from 'vue'

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

</script>

<template>
  <div class="side-nav flex-auto w-full max-w-xl pl-3 shadow-inner">
    <div class="header mb-9">
      <a href="#/">ARCHIVE</a>
    </div>

    <div class="menu text-6xl mt-16">
      <div id="flow-field" class="menu-item">
        <a href="#/flowfield">FLOW FIELD</a>
      </div>
      <div id="watercolor" class="menu-item">
        <a href="#/watercolor">WATERCOLOR</a>
      </div>
      <div id="mondrian" class="menu-item">
        <a href="#/mondrian">MONDRIAN GRID</a>
      </div>
   
    </div>
  </div>
</template>

<style lang="scss">
@import '../styles/sideNav.scss';
</style>