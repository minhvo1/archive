<script setup>
import { library } from '@fortawesome/fontawesome-svg-core'
import { faGithub } from '@fortawesome/free-brands-svg-icons'
library.add(faGithub)

  if (document.getElementById('button-wrapper')) {
    document.getElementById('button-wrapper').style.display = 'none'
  }
</script>

<template>
  <div class="home text-right pr-7 pl-48 h-screen flex flex-col justify-start pt-4">
    <p class="home-text">A COLLECTION OF GENERATIVE ART SKETCHES AND ALGORITHMS.</p>
    <div class="flex justify-end mt-12">
      <a href="https://github.com/minhvo1/archive" target="_blank"><font-awesome-icon icon="fa-brands fa-github"/></a>
    </div>
  </div>
</template>

<style lang="scss">
@import '../styles/home.scss';
</style>