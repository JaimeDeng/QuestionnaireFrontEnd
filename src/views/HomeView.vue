<script>
import navBar from "../components/navBar.vue";
export default (await import('vue')).defineComponent({
components: {
  navBar
},
data() {
  return{
    perPage: 3,
    currentPage: 1,
    items: [
      { Qid: 1, title: 'Fred', status: 'Flintstone', startTime: '2023/06/10', endTime: '2023/06/15'},
      { Qid: 2, title: 'Wilma', status: 'Flintstone', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 3, title: 'Barney', status: 'Rubble', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 4, title: 'Betty', status: 'Rubble', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 5, title: 'Pebbles', status: 'Flintstone', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 6, title: 'Bamm Bamm', status: 'Rubble', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 7, title: 'The Great', status: 'Gazzoo', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 8, title: 'Rockhead', status: 'Slate', startTime: '2023/06/10', endTime: '2023/06/15' },
      { Qid: 9, title: 'Pearl', status: 'Slaghoople', startTime: '2023/06/10', endTime: '2023/06/15' }
    ]
  }
},
computed: {
  rows() {
    return this.items.length
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
    <navBar />

    <!--搜尋欄-->
    <div class="searchFrame">
      <div class="keywordFrame">
        <input type="text" name="keyword" id="keyword" class="keyword">
      </div>
      <div class="timeFrame">
        <input type="date" id="startTime" class="startTime">
        <input type="date" id="endTime" class="endTime">
      </div>
    </div>

    <div class="listFrame">

      <!--列表-->
      <table class="table table-striped table-hover">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">問卷</th>
            <th scope="col">狀態</th>
            <th scope="col">開始時間</th>
            <th scope="col">結束時間</th>
            <th scope="col">觀看統計</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="{item , index} in items" :key="index">
            <th scope="row">{{ item.Qid }}</th>
            <td>{{ item.title }}</td>
            <td>{{ item.status }}</td>
            <td>{{ item.startTime }}</td>
            <td>{{ item.endTime }}</td>
            <td><a href="#">統計</a></td>
          </tr>
        </tbody>
      </table>

      <!--頁面索引-->
      <nav class="navigation" aria-label="Page navigation navigation">
        <ul class="pagination">
          <li class="page-item">
            <a class="page-link" href="#" aria-label="Previous">
              <span aria-hidden="true">&laquo;</span>
            </a>
          </li>
          <li class="page-item"><a class="page-link" href="#">1</a></li>
          <li class="page-item"><a class="page-link" href="#">2</a></li>
          <li class="page-item"><a class="page-link" href="#">3</a></li>
          <li class="page-item">
            <a class="page-link" href="#" aria-label="Next">
              <span aria-hidden="true">&raquo;</span>
            </a>
          </li>
        </ul>
      </nav>

    </div> <!--listFrame-->

  </main>
</template>

<style lang="scss" scoped>
@import '../assets/color';
  main{
    position: relative;
    height: 100vh;
    background-color: $page;

    .searchFrame{
      position: relative;
      border-radius: 1vh;
      background-color: rgba($color: #757373, $alpha: 0.5);
      margin-left: auto;
      margin-right: auto;
      height: 20%;
      width: 50%;

      input{
        border-radius: 0.5vh;
        border: none;
      }
    }
    .listFrame{
      position: relative;
      background-color: rgba($color: #757373, $alpha: 0.5);
      margin-left: auto;
      margin-right: auto;
      height: 50%;
      width: 70%;
      .table{
        width: 100%;
        height: 80%;
      }
      .navigation{
        width: max-content;
        margin-left: 50%;
        transform: translateX(-50%);
      }
    }
  }
</style>