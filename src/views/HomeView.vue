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
    offsetTable: [],
    keyword: '',
    startTime: '',
    endTime: '',
    notRenderOver: true,
    pageNotRenderOver: true,
    noInfo: false
  }
},
methods: {
  setEmptyTable(num){
    this.offsetTable = [];
    if(num !== 5 || num % 10 !== 5){
      let offset = 0;
      if(num > 9){
        num = num % 10;
      }
      if(num < 5){
        offset = Math.abs(num - 5);
      }
      if(num > 5){
        offset = 5 - (num - 5);
      }
      while(offset > 0){
        this.offsetTable.push(offset);
        offset--;
      }
    }
  },
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
      if(data.dataNum !== 0){
        this.noInfo = false;
      }else{
        this.noInfo = true;
      }
      this.setEmptyTable(data.dataNum);
      this.renderPagenation();
    })
    .catch(err => console.log(err))
  },
  getDataNumInCriteria(keyword, startTime , endTime){
    fetch(`http://localhost:3000/getQuestionnaireNumInCriteria/${keyword}/${startTime}/${endTime}` ,{
        method:"get",
        headers: {
            'Content-Type': 'application/json; charset=utf-8'
        }
    }).then(res => res.json())
    .then((data)=>{
      console.log(data.dataNum);
      this.questionnaireDataNum = data.dataNum;
      this.setEmptyTable(data.dataNum);
      this.renderPagenation();
      if(data.dataNum !== 0){
        this.noInfo = false;
        this.initCriteriaPage(keyword , startTime , endTime);
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
      this.pageNotRenderOver = false;
    })
    .catch(err => console.log(err))
  },
  initCriteriaPage(keyword , startTime , endTime){
    fetch(`http://localhost:3000/getQuestionnairesInCriteria/${keyword}/${startTime}/${endTime}/1` ,{
        method:"get",
        headers: {
            'Content-Type': 'application/json; charset=utf-8'
        }
    }).then(res => res.json())
    .then((data)=>{
      this.formatTime(data.questionnaires);
      this.questionnaires = data.questionnaires;
      this.setStatus();
      this.pageNotRenderOver = false;
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
        this.pageNotRenderOver = true;
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
            this.pageNotRenderOver = false;
          })
          .catch(err => console.log(err))
      }
    }
    if(this.keyword !== '' && this.startTime === '' && this.endTime === ''){
      if(index > 0 && index <= this.pageNum){
        this.pageNotRenderOver = true;
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
            this.pageNotRenderOver = false;
          })
          .catch(err => console.log(err))
      }
    }
    if(this.keyword === '' && this.startTime !== '' || this.endTime !== ''){
      if(index > 0 && index <= this.pageNum){
        this.pageNotRenderOver = true;
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
            this.pageNotRenderOver = false;
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
    if(this.startTime === '' && this.endTime === '' && newKeyword === ''){
      this.noInfo = false;
      this.getDataNum();
      this.initPage();
    }else if(this.startTime === '' && this.endTime === ''){
      this.getDataNumInCriteria(newKeyword , null , null);
    }else if(this.startTime !== '' && this.endTime === ''){
      this.getDataNumInCriteria(newKeyword , this.startTime , null);
    }else if(this.startTime === '' && this.endTime !== ''){
      this.getDataNumInCriteria(newKeyword , null , this.endTime);
    }else if(this.startTime !== '' && this.endTime !== ''){
      this.getDataNumInCriteria(newKeyword , this.startTime , this.endTime);
    }
  },
  //開始時間欄位
  startTime(newTime){
    if(newTime === '' && this.endTime === '' && this.keyword === ''){
      //若結束時間與關鍵字是空的時 , 清空開始時間
      this.noInfo = false;
      this.currentPage = 1;
      this.getDataNum();
      this.initPage();
    }else if(this.endTime === '' && this.keyword === ''){
      //若結束時間與關鍵字是無值 , 輸入開始時間
      this.getDataNumInCriteria(null , newTime , null);
    }else if(this.endTime !== '' && this.keyword === ''){
      //若結束時間是有值 , 輸入開始時間
      let startTime = new Date(newTime);
      let endTime = new Date(this.endTime);
      if(startTime > endTime){
        window.alert("開始時間不得在結束時間之後")
        this.startTime = '';
        this.endTime = '';
      }else{
        this.getDataNumInCriteria(null , newTime , this.endTime);
      }
    }else if(this.endTime === '' && this.keyword !== ''){
      //若關鍵字有值結束時間為空 , 輸入結束時間
      this.getDataNumInCriteria(this.keyword , newTime , null);
    }else if(this.endTime !== '' && this.keyword !== ''){
      //若結束時間與關鍵字是有值 , 輸入開始時間
      let startTime = new Date(newTime);
      let endTime = new Date(this.endTime);
      if(startTime > endTime){
        window.alert("開始時間不得在結束時間之後")
        this.keyword = '';
        this.startTime = '';
        this.endTime = '';
      }else{
        this.getDataNumInCriteria(this.keyword , newTime , this.endTime);
      }
    }
  },
  //結束時間欄位
  endTime(newTime){
    if(this.startTime === '' && newTime === '' && this.keyword === ''){
      //若開始時間與關鍵字是空的時 , 清空結束時間
      this.noInfo = false;
      this.currentPage = 1;
      this.getDataNum();
      this.initPage();
    }else if(this.startTime === '' && this.keyword === ''){
      //若開始時間與關鍵字是無值 , 輸入結束時間
      this.getDataNumInCriteria(null , null , newTime);
    }else if(this.startTime !== '' && this.keyword === ''){
      //若開始時間有值 , 輸入結束時間
      let startTime = new Date(this.startTime);
      let endTime = new Date(newTime);
      if(startTime > endTime){
        window.alert("結束時間不得在結束時間之前")
        this.startTime = '';
        this.endTime = '';
      }else{
        this.getDataNumInCriteria(null , this.startTime , newTime);
      }
    }else if(this.startTime === '' && this.keyword !== ''){
      //若關鍵字有值開始時間為空 , 輸入結束時間
      this.getDataNumInCriteria(this.keyword , null , newTime);
    }else if(this.startTime !== '' && this.keyword !== ''){
      //若開始時間與關鍵字是有值 , 輸入結束時間
      let startTime = new Date(this.startTime);
      let endTime = new Date(newTime);
      if(startTime > endTime){
        window.alert("開始時間不得在結束時間之後")
        this.keyword = '';
        this.startTime = '';
        this.endTime = '';
      }else{
        this.getDataNumInCriteria(this.keyword , newTime , this.endTime);
      }
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
        <div v-if="this.pageNotRenderOver === true" class="spinner-grow text-secondary pageNotRenderOverSpinner" role="status"></div>
        <thead v-if="this.pageNotRenderOver === false">
          <tr>
            <th scope="col">#</th>
            <th scope="col">問卷主題</th>
            <th scope="col">狀態</th>
            <th scope="col">開始時間</th>
            <th scope="col">結束時間</th>
            <th scope="col">觀看統計</th>
          </tr>
        </thead>
        <tbody v-if="this.pageNotRenderOver === false">
          <tr v-for="(questionnaire , index) in questionnaires" :key="index">
            <th class="Qid" scope="row">{{ questionnaire.questionnaireId }}</th>
            <td v-if="questionnaire.status === '進行中'" @click="checkOut(questionnaire.questionnaireId)" class="fw-bold questionnaireTitle">{{ questionnaire.title }}</td>
            <td v-else style="color:rgb(179, 179, 179)" class="questionnaireTitle">{{ questionnaire.title }}</td>
            <td class="status" :style="{color : questionnaire.status === '進行中' ? '#20b52f' : questionnaire.status === '尚未開始' ? 'gray' : questionnaire.status === '已結束' ? '#284c8a' : '' ,
            fontWeight :'bold'}">{{ questionnaire.status }}</td>
            <td>{{ questionnaire.startTime }}</td>
            <td>{{ questionnaire.endTime }}</td>
            <td v-if="questionnaire.status === '進行中' || questionnaire.status === '已結束'"><a class="text-decoration-none" href="#">統計結果</a></td>
            <td v-else></td>
          </tr>
          <tr v-if="this.currentPage === this.pageNum" v-for="(offset , index) in offsetTable" :key="index">
            <th class="Qid" scope="row"></th>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
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
        .pageNotRenderOverSpinner{
          margin: 0;
          position: absolute;
          top: 50%;
          transform: translateY(-50%);
          left: 49%;
          transform: translateX(-50%);
        }

        .questionnaireTitle{
          &:hover{
            color: #b99b3b;
          }
        }

        tbody , thead{
          font-size: 1.5vh;

          td{
            cursor: pointer;
            height: 5.5vh;
            vertical-align: middle;
          }
          th{
            cursor: pointer;
            height: 2.5vh;
            vertical-align: middle;
          }
          .questionnaireTitle{
            width: 30vw;
          }
          .status{
            width: 10vw;
          }
          .Qid{
            cursor: pointer;
            height: 5.5vh;
            vertical-align: middle;
          }
        }
      }
      .page{
        position: absolute;
        width: max-content;
        left: 50%;
        bottom: 12%;
        transform: translateX(-50%);
      }
      .navigation{
        position: absolute;
        width: max-content;
        left: 50%;
        bottom: 0.5%;
        transform: translateX(-50%);

        .page-link{
          cursor: pointer;
        }
      }
    }
  }
</style>