<script>
export default (await import('vue')).defineComponent({
components: {
},
data() {
  return{
    questionnaireDataNum: 0,
    pageNum: 0,
    pageIndex: [],
    perPage: 3,
    currentPage: 1,
    questionnaires: [],
    keyword: '',
    items: [
      { Qid: 1, title: 'Fred', status: '進行中', startTime: '2023/06/10', endTime: '2023/06/15'},
      { Qid: 2, title: 'Wilma', status: '進行中', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 3, title: 'Barney', status: '已結束', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 4, title: 'Betty', status: '進行中', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 5, title: 'Pebbles', status: '進行中', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 6, title: 'Bamm Bamm', status: '尚未開始', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 7, title: 'The Great', status: '進行中', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 8, title: 'Rockhead', status: '已結束', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 9, title: 'Pearl', status: '已結束', startTime: '2023/06/10', endTime: '2023/06/15' }
    ]
  }
},
methods: {
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
          this.initKeywordPage(keyword);
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
          this.questionnaires = data.questionnaires;
          console.log(data.questionnaires);
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
          this.questionnaires = data.questionnaires;
          console.log(data.questionnaires);
        })
        .catch(err => console.log(err))
  },
  changePage(index){
    if(this.keyword === ''){
      if(index > 0 && index <= this.pageNum){
        this.currentPage = index;
        fetch(`http://localhost:3000/getQuestionnairesByPage/${this.currentPage}` ,{
              method:"get",
              headers: {
                  'Content-Type': 'application/json; charset=utf-8'
              }
          }).then(res => res.json())
          .then((data)=>{
            this.questionnaires = data.questionnaires;
            console.log(data.questionnaires);
          })
          .catch(err => console.log(err))
      }
    }else{
      if(index > 0 && index <= this.pageNum){
        this.currentPage = index;
        fetch(`http://localhost:3000/getQuestionnaireByKeywordAsPage/${this.keyword}/${this.currentPage}` ,{
              method:"get",
              headers: {
                  'Content-Type': 'application/json; charset=utf-8'
              }
          }).then(res => res.json())
          .then((data)=>{
            this.questionnaires = data.questionnaires;
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
  keyword(newKeyword){
    console.log(newKeyword);
    if(newKeyword === ''){
      this.getDataNum();
      this.initPage();
    }else{
      this.getDataNumByKeyword(newKeyword);
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
    <div class="searchFrame">
      <div class="keywordFrame">
        <label for="keyword">關鍵字搜尋</label>
        <input v-model="keyword" type="text" name="keyword" id="keyword" class="keyword">
      </div>
      <div class="timeFrame">
        <label for="startTime" class="startTimeLb">開始時間</label>
        <input type="date" id="startTime" class="startTime">
        <label for="endTime" class="endTimeLb">結束時間</label>
        <input type="date" id="endTime" class="endTime">
      </div>
    </div>

    <div class="listFrame">

    <!--列表-->
    <h1>問卷一覽</h1>
    <table class="table table-striped table-hover">
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
        <tr @click="checkOut(questionnaire.questionnaireId)" v-for="(questionnaire , index) in questionnaires" :key="index">
          <th scope="row">{{ questionnaire.questionnaireId }}</th>
          <td class="fw-bold">{{ questionnaire.title }}</td>
          <td>{{ "沒" }}</td>
          <td>{{ questionnaire.startTime }}</td>
          <td>{{ questionnaire.endTime }}</td>
          <td><a href="#">統計</a></td>
        </tr>
      </tbody>
    </table>

    <!--頁面索引-->
    <h6 class="page">{{ this.currentPage }}</h6>
    <nav class="navigation" aria-label="Page navigation navigation">
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
      border: 0.5px solid gray;
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
        border: 1px solid #D2E9E9;
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

    .listFrame{
      position: relative;
      background-color: rgba($color: #D2E9E9, $alpha: 0.3);
      border-radius: 1vh;
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
      .table-hover{
        position: relative;
        border: 1px solid rgb(192, 191, 191);
        margin-left: auto;
        margin-right: auto;
        width: 90%;
        height: 30vh;

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