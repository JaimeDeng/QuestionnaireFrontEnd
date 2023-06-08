<script>
export default (await import('vue')).defineComponent({
components: {
},
data() {
  return{
    questionnaireDataNum: 0,
    pageNum: 0,
    pageIndex: [],
    currentPage: 1,
    questionnaires: [],
    keyword: '',
    startTime: '',
    endTime: '',
    notRenderOver: true,
    noInfo: false
  }
},
methods: {
  formatTime(questionnaires){
    questionnaires.forEach((questionnaire) => {
      var startTime = new Date(questionnaire.startTime);
      var endTime = new Date(questionnaire.endTime);
      var startYear = startTime.getFullYear();
      var startMonth = startTime.getMonth() + 1;
      startMonth < 10 ? startMonth = "0" + startMonth : startMonth.toString();
      var startDay = startTime.getDate();
      startDay < 10 ? startDay = "0" + startDay : startDay.toString();

      var endYear = endTime.getFullYear();
      var endMonth = endTime.getMonth() + 1;
      endMonth < 10 ? endMonth = "0" + endMonth : endMonth.toString();
      var endDay = endTime.getDate();
      endDay < 10 ? endDay = "0" + endDay : endDay.toString();

      var formattedStartTime = startYear + "-" + startMonth + "-" + startDay;
      var formattedEndime = endYear + "-" + endMonth + "-" + endDay;
      questionnaire.startTime = formattedStartTime;
      questionnaire.endTime = formattedEndime;
    });
  },
  getDataNum(){
    fetch("http://localhost:3000/getQuestionnairesDataNum" ,{
        method:"get",
        headers: {
            'Content-Type': 'application/json; charset=utf-8'
        }
    }).then(res => res.json())
    .then((data)=>{
      console.log(data.dataNum);
      this.questionnaireDataNum = data.dataNum;
      this.renderPagenation();
    })
    .catch(err => console.log(err))
  },
  getDataNumByKeyword(keyword){
    fetch(`http://localhost:3000/getQuestionnaireNumByKeyword/${keyword}` ,{
        method:"get",
        headers: {
            'Content-Type': 'application/json; charset=utf-8'
        }
    }).then(res => res.json())
    .then((data)=>{
      console.log(data.dataNum);
      this.questionnaireDataNum = data.dataNum;
      this.renderPagenation();
      if(data.dataNum !== 0){
        this.initKeywordPage(keyword);
      }else{
        this.noInfo = true;
      }
    })
    .catch(err => console.log(err))
  },
  getDataNumInTimeFrame(startTime , endTime){
    fetch(`http://localhost:3000/getQuestionnaireNumInTimeFrame/${startTime}/${endTime}` ,{
        method:"get",
        headers: {
            'Content-Type': 'application/json; charset=utf-8'
        }
    }).then(res => res.json())
    .then((data)=>{
      console.log(data.dataNum);
      this.questionnaireDataNum = data.dataNum;
      this.renderPagenation();
      if(data.dataNum !== 0){
        this.initTimeFramePage(startTime , endTime);
      }else{
        this.noInfo = true;
      }
    })
    .catch(err => console.log(err))
  },
  renderPagenation(){
    this.pageIndex = [];
    this.pageNum = Math.ceil(this.questionnaireDataNum / 5);
    console.log(this.pageNum);
    for(let i = 1 ; i <= this.pageNum ; i++){
      this.pageIndex.push(i);
    }
    console.log(this.pageIndex);
  },
  initPage(){
    fetch(`http://localhost:3000/getQuestionnairesByPage/1` ,{
        method:"get",
        headers: {
            'Content-Type': 'application/json; charset=utf-8'
        }
    }).then(res => res.json())
    .then((data)=>{
      this.formatTime(data.questionnaires);
      this.questionnaires = data.questionnaires;
      this.setStatus();
      console.log(data.questionnaires);
      this.notRenderOver = false;
    })
    .catch(err => console.log(err))
  },
  initKeywordPage(keyword){
    fetch(`http://localhost:3000/getQuestionnaireByKeywordAsPage/${keyword}/1` ,{
        method:"get",
        headers: {
            'Content-Type': 'application/json; charset=utf-8'
        }
    }).then(res => res.json())
    .then((data)=>{
      this.formatTime(data.questionnaires);
      this.questionnaires = data.questionnaires;
      this.setStatus();
      console.log(data.questionnaires);
    })
    .catch(err => console.log(err))
  },
  initTimeFramePage(startTime , endTime){
    fetch(`http://localhost:3000/getQuestionnairesInTimeFrame/${startTime}/${endTime}/1` ,{
        method:"get",
        headers: {
            'Content-Type': 'application/json; charset=utf-8'
        }
    }).then(res => res.json())
    .then((data)=>{
      this.formatTime(data.questionnaires);
      this.questionnaires = data.questionnaires;
      this.setStatus();
      console.log(data.questionnaires);
    })
    .catch(err => console.log(err))
  },
  setStatus(){
    let today = new Date();
    this.questionnaires.forEach((questionnaire) => {
      let startTime = new Date(questionnaire.startTime);
      let endTime = new Date(questionnaire.endTime);
      if(today > startTime && today < endTime){
        questionnaire.status = '進行中';
      }
      if(today < startTime){
        questionnaire.status = '尚未開始';
      }
      if(today > endTime){
        questionnaire.status = '已結束';
      }
    })
    console.log(this.questionnaires);
  },
  changePage(index){
    if(this.keyword === '' && this.startTime === '' && this.endTime === ''){
      if(index > 0 && index <= this.pageNum){
        this.currentPage = index;
        fetch(`http://localhost:3000/getQuestionnairesByPage/${this.currentPage}` ,{
              method:"get",
              headers: {
                  'Content-Type': 'application/json; charset=utf-8'
              }
          }).then(res => res.json())
          .then((data)=>{
            this.formatTime(data.questionnaires);
            this.questionnaires = data.questionnaires;
            this.setStatus();
            console.log(data.questionnaires);
          })
          .catch(err => console.log(err))
      }
    }
    if(this.keyword !== '' && this.startTime === '' && this.endTime === ''){
      if(index > 0 && index <= this.pageNum){
        this.currentPage = index;
        fetch(`http://localhost:3000/getQuestionnaireByKeywordAsPage/${this.keyword}/${this.currentPage}` ,{
              method:"get",
              headers: {
                  'Content-Type': 'application/json; charset=utf-8'
              }
          }).then(res => res.json())
          .then((data)=>{
            this.formatTime(data.questionnaires);
            this.questionnaires = data.questionnaires;
            this.setStatus();
            console.log(data.questionnaires);
          })
          .catch(err => console.log(err))
      }
    }
    if(this.keyword === '' && this.startTime !== '' || this.endTime !== ''){
      if(index > 0 && index <= this.pageNum){
        this.currentPage = index;
        let startTime = this.startTime;
        let endTime = this.endTime;
        if(this.startTime === ''){
          startTime = null;
        }
        if(this.endTime === ''){
          endTime = null;
        }
        fetch(`http://localhost:3000/getQuestionnairesInTimeFrame/${startTime}/${endTime}/${this.currentPage}` ,{
              method:"get",
              headers: {
                  'Content-Type': 'application/json; charset=utf-8'
              }
          }).then(res => res.json())
          .then((data)=>{
            this.formatTime(data.questionnaires);
            this.questionnaires = data.questionnaires;
            this.setStatus();
            console.log(data.questionnaires);
          })
          .catch(err => console.log(err))
      }
    }
  },
  checkOut(id){
    console.log(id);
    this.$router.push({ path: `/Questionnaire/${id}`});
  }
},
watch:{
  //關鍵字欄位
  keyword(newKeyword){
    console.log(newKeyword);
    if(newKeyword === ''){
      this.noInfo = false;
      this.getDataNum();
      this.initPage();
    }else{
      this.getDataNumByKeyword(newKeyword);
    } 
  },
  //開始時間欄位
  startTime(newTime){
    if(newTime === ''){
      this.noInfo = false;
      this.getDataNum();
      this.initPage();
    }else if(this.endTime === ''){
      this.getDataNumInTimeFrame(newTime , null);
    }else if(this.endTime !== ''){
      this.getDataNumInTimeFrame(newTime , this.endTime);
    }
  },
  //結束時間欄位
  endTime(newTime){
    if(newTime === ''){
      this.noInfo = false;
      this.getDataNum();
      this.initPage();
    }else if(this.startTime === ''){
      this.getDataNumInTimeFrame(null , newTime);
    }else if(this.startTime !== ''){
      this.getDataNumInTimeFrame(this.startTime , newTime);
    }
  }
},
mounted() {
  this.getDataNum();
  this.initPage();
}
})
</script>

<template>
  <div class="frame">

    <!--搜尋欄-->
    <div v-if="!notRenderOver" class="searchFrame">
      <div class="keywordFrame">
        <label for="keyword">關鍵字搜尋</label>
        <input v-model="keyword" type="text" name="keyword" id="keyword" class="keyword">
      </div>
      <div class="timeFrame">
        <label for="startTime" class="startTimeLb">開始時間</label>
        <input v-model="startTime" type="date" id="startTime" class="startTime">
        <label for="endTime" class="endTimeLb">結束時間</label>
        <input v-model="endTime" type="date" id="endTime" class="endTime">
      </div>
    </div>

    <div v-if="notRenderOver" class="spinner-grow text-secondary" role="status">
      <span class="visually-hidden">Loading...</span>
    </div>
    <div v-else class="listFrame">
      <!--列表-->
      <h1>問卷一覽 <font-awesome-icon class="fa-check-to-slot" icon="fa-solid fa-check-to-slot" /></h1>
      <h1 class="noInfoText" v-if="noInfo">無此條件的問卷存在</h1>
      <table v-if="!noInfo" class="table table-striped table-hover">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">問卷主題</th>
            <th scope="col">狀態</th>
            <th scope="col">開始時間</th>
            <th scope="col">結束時間</th>
            <th scope="col">觀看統計</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(questionnaire , index) in questionnaires" :key="index">
            <th scope="row">{{ questionnaire.questionnaireId }}</th>
            <td v-if="questionnaire.status === '進行中'" @click="checkOut(questionnaire.questionnaireId)" class="fw-bold questionnaireTitle">{{ questionnaire.title }}</td>
            <td v-else style="color:rgb(179, 179, 179)">{{ questionnaire.title }}</td>
            <td :style="{color : questionnaire.status === '進行中' ? '#20b52f' : questionnaire.status === '尚未開始' ? 'gray' : questionnaire.status === '已結束' ? '#284c8a' : '' ,
            fontWeight :'bold'}">{{ questionnaire.status }}</td>
            <td>{{ questionnaire.startTime }}</td>
            <td>{{ questionnaire.endTime }}</td>
            <td v-if="questionnaire.status === '進行中' || questionnaire.status === '已結束'"><a href="#">統計</a></td>
            <td v-else></td>
          </tr>
        </tbody>
      </table>

      <!--頁面索引-->
      <h6 v-if="!noInfo" class="page">{{ this.currentPage }}</h6>
      <nav v-if="!noInfo" class="navigation" aria-label="Page navigation navigation">
        <ul class="pagination">
          <li class="page-item">
            <a class="page-link"  @click="changePage(this.currentPage - 1)" aria-label="Previous">
              <span aria-hidden="true">&laquo;</span>
            </a>
          </li>
          <li v-for="(index) in pageIndex" class="page-item">
            <a class="page-link" :value="index" @click="changePage(index)">{{ index }}</a>
          </li>
          <li class="page-item">
            <a class="page-link" @click="changePage(this.currentPage + 1)" aria-label="Next">
              <span aria-hidden="true">&raquo;</span>
            </a>
          </li>
        </ul>
      </nav>

    </div> <!--listFrame-->

  </div>
</template>

<style lang="scss" scoped>
@import '../assets/color';
  .frame{
    position: relative;
    .searchFrame{
      position: relative;
      border-radius: 1vh;
      border: 1px solid rgba(255, 255, 255, 0.8);
      background-color: rgba($color: #D2E9E9, $alpha: 0.5);
      margin-left: auto;
      margin-right: auto;
      margin-top: 5vh;
      margin-bottom: 5vh;
      height: max-content;
      width: 35%;
      padding-bottom: 2vh;

      input{
        height: 3vh;
        width: 10vw;
        border-radius: 0.5vh;
        border: 1px solid #d2dae9;
      }

      label{
        line-height: 3vh;
        margin-right: 0.5vw;
      }
      .keywordFrame{
        display: flex;
        margin-top: 2%;
        justify-content: center;
        .keyword{
          width: 50%;
        }
      }
      .timeFrame{
        display: flex;
        margin-top: 2%;
        justify-content: center;
        .endTimeLb{
          margin-left: 1vw;
        }
      }
    }

    .text-secondary{
      height: 3rem;
      width: 3rem;
      margin-left: 50%;
      margin-top: 20%;
      transform: translateX(-50%);
    }

    .listFrame{
      position: relative;
      background: linear-gradient(to top, rgba($color: #86c3c3, $alpha: 0.3), rgba($color: #D2E9E9, $alpha: 0.3));
      backdrop-filter: blur(5px);
      border-radius: 5vh;
      border: 1px solid rgba(255, 255, 255, 0.8);
      box-shadow: 0px 0px 50px 5px rgba(199, 199, 199, 0.5);
      margin-left: auto;
      margin-right: auto;
      padding-top: 2%;
      height: 50vh;
      width: 80%;

      h1{
        font-size: 3vh;
        color: #657c7c;
        margin-left: 4vw;
      }
      .noInfoText{
        width: max-content;
        margin-left: 50%;
        margin-top: 10%;
        transform: translateX(-50%);
      }
      .table-hover{
        position: relative;
        border: 1px solid rgb(192, 191, 191);
        margin-left: auto;
        margin-right: auto;
        width: 90%;
        height: 30vh;

        .questionnaireTitle{
          &:hover{
            color: #b99b3b;
          }
        }

        td{
          cursor: pointer;
        }

        tbody , thead{
          font-size: 1.5vh;
          height: 100%;
        }
      }
      .page{
        width: max-content;
        margin-left: 50%;
        margin-top: 1%;
        transform: translateX(-50%);
      }
      .navigation{
        width: max-content;
        margin-left: 50%;
        margin-top: 1%;
        transform: translateX(-50%);

        .page-link{
          cursor: pointer;
        }
      }
    }
  }
</style>