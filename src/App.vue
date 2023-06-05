<script>
import { RouterLink, RouterView } from 'vue-router'
import navBar from "./components/navBar.vue";
import footer from "./components/footer.vue"
export default (await import('vue')).defineComponent({
components: {
  navBar,
  footer
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
  <navBar />
  <RouterView />
  <footer />
</template>

<style lang="scss" scoped>
</style>
