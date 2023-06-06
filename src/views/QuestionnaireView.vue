<script>
export default (await import('vue')).defineComponent({
components: {
},
data() {
  return{
    questionnaireId : 0
  }
},
methods: {
  checkOut(id){
    console.log(id);
  }
},
mounted() {
  this.questionnaireId = this.$route.params.id
  console.log(this.questionnaireId);
}
})
</script>

<template>
  <div class="frame">

  </div>
</template>

<style lang="scss" scoped>
@import '../assets/color';
  .frame{
    position: relative;
  }
</style>