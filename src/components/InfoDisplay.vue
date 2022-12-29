<template>
  <div class="display flex-auto">
    <WatercolorCanvas/>

  </div>
</template>

<script>
// import FlowFieldCanvas from './FlowFieldCanvas.vue';
import WatercolorCanvas from './WatercolorCanvas.vue';

export default {
  name: 'InfoDisplay',
  components: {
    WatercolorCanvas
  }
}
</script>

<style lang="scss">
  @import '../styles/infoDisplay.scss';
</style>
