<script setup>
import SideNav from './SideNav.vue';
import InfoDisplay from './InfoDisplay.vue';

import { ref } from 'vue'
const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})
</script>

<template>
  <main class="flex w-full h-screen">
    <SideNav/>
    <InfoDisplay path=currentPath />
  </main>
</template>
