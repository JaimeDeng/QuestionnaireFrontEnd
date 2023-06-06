<script>
export default (await import('vue')).defineComponent({
components: {
},
data() {
  return{
    questionnaireId : 0,
    questions : []
  }
},
methods: {
  checkOut(id){
    fetch(`http://localhost:3000/getQuestionsOfQuestionnaire/${id}` ,{
          method:"get",
          headers: {
              'Content-Type': 'application/json; charset=utf-8'
          }
      }).then(res => res.json())
      .then((data)=>{
        this.questions = data.questions;
        console.log(data.questions);
      })
      .catch(err => console.log(err))
  }
},
mounted() {
  this.questionnaireId = this.$route.params.id
  console.log(this.questionnaireId)
  this.checkOut(this.questionnaireId);
}
})
</script>

<template>
  <div class="frame">
    <div v-for="(question , index) in this.questions" class="questionFrame">
      <h5 class="questionTitle">{{ question.question }}</h5>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import '../assets/color';
  .frame{
    margin: auto;
    width: 70%;
    height: 100%;
    position: relative;
    overflow: auto;

    .questionFrame{
      margin-top: 5%;
      margin-left: 10%;
    }
  }
</style>