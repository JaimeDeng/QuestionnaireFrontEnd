<script>
export default (await import('vue')).defineComponent({
components: {
},
data() {
  return{
    questionnaireId : 0,
    questionnaire : [],
    questions : '',
    optionList : [],
    notRenderOver : true,
    noQuestions : false,
    info : [{label : "姓名" , value : ""},
            {label : "電話" , value : ""},
            {label : "信箱" , value : ""},
            {label : "年齡" , value : ""}]
  }
},
methods: {
  checkOut(id){
      fetch(`http://localhost:3000/getQuestionnaireByQuestionnaireId/${id}` ,{
          method:"get",
          headers: {
              'Content-Type': 'application/json; charset=utf-8'
          }
      }).then(res => res.json())
      .then((data)=>{
        this.questionnaire = data;
        console.log(data);

        fetch(`http://localhost:3000/getQuestionsOfQuestionnaire/${id}` ,{
          method:"get",
          headers: {
              'Content-Type': 'application/json; charset=utf-8'
          }
        }).then(res => res.json())
        .then((data)=>{
          this.questions = data.questions;
          console.log(this.questions);
          if(this.questions === undefined){
            this.noQuestions = true;
          }else{
            this.questions.forEach((question) => {
            if(question.type === 'select'){
              let options = question.options.split(",");
              this.optionList.push(options);
            }else{
              this.optionList.push(null);
            }
            });
            console.log(this.optionList);
            this.notRenderOver = false;
          }
        })
        .catch(err => console.log(err))
        })
        .catch(err => console.log(err))
  },
  back(){
    this.$router.push('/');
  }
},
watch:{
  info:{
    
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
  <div v-if="notRenderOver && !noQuestions" class="spinner-grow text-secondary" role="status"></div>
  <div v-else class="frame">
    <h1 class="questionnaireTitle">{{ this.questionnaire.title }}</h1>
    <h5 class="description">{{ this.questionnaire.description }}</h5>
    <h2 class="noQuestionsText" v-if="noQuestions">作者還沒有給這個問卷設定問題喔!</h2>
    <h5 v-if="!noQuestions" class="infoTitle">填答者資訊</h5>
    <div v-if="!noQuestions" v-for="(info , infoIndex) in this.info" class="infoFrame">
      <label class="infoLabel" :for="'input' + infoIndex">{{ info.label }}</label>
      <input v-model="info.value" type="text" :id="'input' + infoIndex">
    </div>
    <div v-for="(question , index) in this.questions" class="questionFrame">
      <h5 :style="{display : question.type === 'select' ? 'inline' : ''}" class="questionTitle">{{ question.question }}</h5><h6 class="required" v-if="question.required"> *必填</h6>
      <div class="checkBoxFrame" v-for="(option , optionIndex) in this.optionList[index]" v-if="question.type === 'select'">
        <input class="form-check-input" type="checkbox" name="flexRadioDefault" :id="'flexRadioDefault' + index + optionIndex">
        <label class="form-check-label option" :for="'flexRadioDefault' + index + optionIndex">{{ option }}</label>
      </div>
      <textarea class="shortAnswer" v-if="question.type === 'short-answer'" ></textarea>
    </div>
    <div class="btnFrame">
      <button class="back" @click="back">返回</button>
      <button class="commit">送出</button>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import '../assets/color';
  .text-secondary{
    height: 3rem;
    width: 3rem;
    margin-left: 50%;
    margin-top: 20%;
    transform: translateX(-50%);
  }
  .frame{
    margin: auto;
    width: 100%;
    height: 80%;
    position: relative;
    overflow: auto;

    //修改瀏覽器的scrollbar樣式
    ::-webkit-scrollbar {
      width: 0.5vw;
    }

    ::-webkit-scrollbar-button {
      background: transparent;
      height: 3%; //上下buffer的高度
      border-radius: 4px;
    }

    ::-webkit-scrollbar-track-piece {
      background: transparent;
    }

    ::-webkit-scrollbar-thumb {
      border-radius: 4px;
      background-color: rgba(129, 91, 21, 0.4);
      border: none;
    }

    ::-webkit-scrollbar-track {
      box-shadow: transparent;
    }

    .noQuestionsText{
      margin-top: 10%;
      margin-left: auto;
      margin-right: auto;
      margin-bottom: 10%;
      width: max-content;
    }

    .questionnaireTitle{
      margin-left: auto;
      margin-right: auto;
      width: max-content;
    }
    .description{
      margin-top: 2%;
      margin-left: auto;
      margin-right: auto;
      width: max-content;
    }
    .infoTitle{
      font-size: 2.2vh;
      margin-left: 35%;
      margin-top: 1%;
    }
    .infoFrame{
      margin-left: 35%;
      margin-top: 1%;

      input{
        margin-left: 2%;
        width: 20vw;
      }
    }
    .questionFrame{
      margin-top: 5%;
      margin-left: 35%;
      .shortAnswer{
        padding: 0.6vh 0.5vw;
        height: 10vh;
        width: 20vw;
        border-radius: 1vh;
        border: 1px solid gray;
        resize: none;
      }
      .questionTitle{
        height: 2vh;
      }
      .required{
        color: rgb(166, 22, 22);
        display: inline;
      }
      .checkBoxFrame{
        position: relative;
        .form-check-input{
          cursor: pointer;
        }
        .option{
          margin-left: 2%;
          font-size: 2vh;
          display: inline;
          width: max-content;
        } 
      }
    }

    .btnFrame{
      width: 20%;
      display: flex;
      justify-content: space-between;
      margin-left: auto;
      margin-right: auto;
      button{
        font-size: 2vh;
        height: 3vh;
        width: 5vw;
        background-color: $button;
        border-radius: 0.8vh;
        border: 1px solid #657c7c;
        transition: 0.4s;
        margin-top: 20%;
        margin-bottom: 20%;
        

        &:hover{
            background-color: #b3c5c5;
        }

        &:active{
            scale: 0.95;
        }
      }
    }
  }
</style>