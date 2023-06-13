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
    //照提默順序其各別的問題ID
    questionIdList: [],
    //照題目順序其各別是否必填
    required: [],
    //照題目順序其各別是否複選
    multipleChoice : [],
    //照題目順序其各別的選擇上限,沒有就是null
    selectLimit: [],
    //綁定page內所有勾選的checkbox值
    checkedList: [],
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
                this.questionIdList.push(question.questionId);
                this.required.push(question.required);
                this.selectLimit.push(question.selectLimit);
                this.optionList.push(options);
                this.multipleChoice.push(question.multipleChoice);
              }else{
                this.optionList.push(null);
                this.questionIdList.push(question.questionId);
                this.required.push(question.required);
                this.selectLimit.push(question.selectLimit);
                this.multipleChoice.push(question.multipleChoice);
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
  check(){
    let hasntFilledIn = [];
    for(let i = 0 ; i < 4 ; i++){
      switch(i){
        case 0:
          if(this.info[i].value === ''){
            hasntFilledIn.push("姓名");
          }
          if(this.info[i].value.length > 20){
            window.alert("姓名不得大於20字符")
          }
          break;
        case 1:
          if(this.info[i].value === ''){
            hasntFilledIn.push("電話");
          }
          let phonePattern = /[0-9]{8,10}/;
          if(!phonePattern.test(this.info[i].value)){
            window.alert("電話格式錯誤");
          }
          break;
        case 2:
          const emailInput = document.getElementById("input2");
          if(this.info[i].value === ''){
            hasntFilledIn.push("email");
          }
          if(!emailInput.checkValidity()){
            window.alert("信箱格式錯誤");
          }
          break;
        case 3:
          if(this.info[i].value === ''){
            hasntFilledIn.push("年齡");
          }
          if(parseInt(this.info[i].value) < 1 || parseInt(this.info[i].value) > 150){
            window.alert("年齡輸入錯誤");
          }
          break;
      }
    }
    if(hasntFilledIn.length !== 0){
      let fillIn = '';
      hasntFilledIn.forEach((item , index)=>{
        if(index !== hasntFilledIn.length - 1){
          fillIn += item + '、';
        }else{
          fillIn += item;
        }
      })
      window.alert("請填寫" + fillIn);
    }
    const shortAns = document.querySelectorAll(`[id^="shortAns"]`);
    console.log(shortAns);
  },
  back(){
    this.$router.push('/');
  }
},
watch:{
  info: {
    deep: true,
    handler(newVal) {
      console.log("新值：", newVal);
    }
  },
  checkedList: {
    deep: true,
    handler(newVal , oldVal) {
      console.log("新值：", newVal);

      //如果是單選題
      this.multipleChoice.forEach((item , index)=>{
        if(item === false){
          this.checkedList.forEach((checked)=>{
            let checkedIndex = checked.split(" ");
            //此題目還存在以勾選選項就跳過
            if(checkedIndex[0] === index){
              return;
            }
          })
          //檢查若此題目為單選題但無已勾選,則通通都解鎖
          const elements = document.querySelectorAll(`[id^="flexRadioDefault${index}"]`);
          elements.forEach((element)=>{
            if(!element.checked){
              element.disabled = false;
            }
          })
        }
      })

      let selectedNum = [];
      this.checkedList.forEach((checked)=>{
        let checkedIndex = checked.split(" ");
        //若是單選題
        if(this.multipleChoice[parseInt(checkedIndex[0])] === false){
          const elements = document.querySelectorAll(`[id^="flexRadioDefault${parseInt(checkedIndex[0])}"]`);
          elements.forEach((element)=>{
            if(!element.checked){
              element.disabled = true;
            }
          })
        }
        selectedNum.push(checkedIndex[0]);
        console.log(selectedNum);
      })

      console.log(this.selectLimit);
      this.selectLimit.forEach((limit , index)=>{
        let count = 0;
        selectedNum.forEach((selected)=>{
          if(selected === index.toString()){
            count++;
          }
        })
        console.log("count = " + count);
        if(count === limit){
          const elements = document.querySelectorAll(`[id^="flexRadioDefault${index}"]`);
          console.log(elements);
          elements.forEach((element)=>{
            if(!element.checked){
              element.disabled = true;
            }
          })
        }else if(count < limit){
          const elements = document.querySelectorAll(`[id^="flexRadioDefault${index}"]`);
          console.log(elements);
          elements.forEach((element)=>{
            if(!element.checked){
              element.disabled = false;
            }
          })
        }
      })
    }
  },
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
      <input v-model="info.value" :type="infoIndex === 2 ? 'email' : 'text'" :id="'input' + infoIndex">
    </div>
    <div v-for="(question , index) in this.questions" class="questionFrame" :id="'questionFrame-' + index">
      <h5 :style="{display : question.type === 'select' ? 'inline' : ''}" class="questionTitle">{{ question.question }}</h5>
      <h6 class="descript" v-if="question.selectLimit !== null"> (選擇上限:{{ question.selectLimit }})</h6>
      <h6 class="descript" v-if="question.required"> *必填</h6>
      <div class="checkBoxFrame" v-for="(option , optionIndex) in this.optionList[index]" v-if="question.type === 'select'">
        <input class="form-check-input" type="checkbox" name="flexRadioDefault" :value="index + ' ' + option" 
        v-model="checkedList" :id="'flexRadioDefault' + index + ' ' + optionIndex">
        <label class="form-check-label option" :for="'flexRadioDefault' + index + ' ' + optionIndex">{{ option }}</label>
      </div>
      <textarea class="shortAnswer" v-if="question.type === 'short-answer'" :id="'shortAns' + ' ' + index"></textarea>
    </div>
    <div class="btnFrame">
      <button class="back" @click="back">返回</button>
      <button class="commit" @click="check">送出</button>
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
      .descript{
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