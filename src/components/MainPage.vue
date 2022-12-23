<template>
  <main class="flex w-full h-screen">
    <SideNav/>
    <InfoDisplay/>
  </main>
</template>

<script>
import SideNav from './SideNav.vue';
import InfoDisplay from './InfoDisplay.vue';

export default {
  name: 'MainPage',
  props: {
    msg: String
  },
  components: {
    SideNav,
    InfoDisplay
  }
}
</script>