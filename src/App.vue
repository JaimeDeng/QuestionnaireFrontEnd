<script>
import { RouterLink, RouterView } from 'vue-router'
import navBar from "./components/navBar.vue";
import footerComponent from "./components/footerComponent.vue"
export default (await import('vue')).defineComponent({
components: {
  navBar,
  footerComponent
},
data() {
  return{
  }
},
methods: {

},
mounted() {

}
})
</script>

<template>
  <main>
    <navBar class="navBar" />
    <RouterView />
    <footerComponent class="footer" />
  </main>
</template>

<style lang="scss" scoped>
  @import './assets/color';
  main{
    position: absolute;
    top: 0;
    margin: 0;
    left: 0;
    right: 0;
    height: 100vh;
    background-color: $page;

    .footer{
      position: absolute;
      bottom: 0;
      width: 100%;
    }
  }
</style>
