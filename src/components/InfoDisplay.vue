<template>
  <div class="display flex-auto">
    <GenCanvas/>

  </div>
</template>

<script>
import GenCanvas from './GenCanvas.vue';

export default {
  name: 'InfoDisplay',
  components: {
    GenCanvas
  }
}
</script>

<style lang="scss">
  @import '../styles/infoDisplay.scss';
</style>
