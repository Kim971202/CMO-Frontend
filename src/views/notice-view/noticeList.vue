<!-- 
    최초 작정자: 김동현
    최초 작성일자: 2022년10월21일

    최근 수정자: 김동현
    최근 수정일자: 2022년10월21일
-->
<template>
  <div class="board">
    <h2>공지사항</h2>
    <div class="common-buttons">
      <button
        type="button"
        class="w3-button w3-round w3-teal"
        v-on:click="fnWrite"
      >
        신규
      </button>
    </div>
    <table>
      <colgroup>
        <col style="width: 15%" />
        <col style="width: *" />
        <col style="width: *" />
        <col style="width: 10%" />
      </colgroup>
      <tbody>
        <tr>
          <th scope="row">조회기간</th>
          <td style="float: center">
            <input
              type="date"
              style="width: 150px; text-align: center"
              v-bind:disabled="moveOutDtime == ''"
              v-model.trim="startDate"
            />
            ~
            <input
              type="date"
              style="width: 150px; text-align: center"
              v-bind:disabled="moveOutDtime == ''"
              v-model.trim="endDate"
            />
          </td>
          <th scope="row">공지유형</th>
          <td>
            <select
              v-model="notiType"
              style="width: 150px; height: 25px; text-align: center"
            >
              <option value="">----전체----</option>
              <option value="전체">전체공지</option>
              <option value="개별">개별공지</option>
            </select>
          </td>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
        </tr>
        <tr>
          <th scope="row">월패드알림</th>
          <td>
            <select
              v-model="sendResult"
              style="width: 150px; height: 25px; text-align: center"
            >
              <option value="">----전체----</option>
              <option value="Y">Y</option>
              <option value="N">N</option>
            </select>
          </td>
          <th scope="row">검색단위</th>
          <td>
            <input
              type="text"
              style="width: 150px; text-align: center"
              ref="sizeInput"
              v-model="size"
              @keyup.enter="fnSearch"
            />
          </td>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
        </tr>
      </tbody>
    </table>
  </div>
  <div class="buttons">
    <div class="right">
      <button class="button blue" @click="fnSearch">검색</button>
      <button class="button" @click="fnList">취소</button>
    </div>
  </div>
  <table class="w3-table-all">
    <colgroup>
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: *" />
    </colgroup>
    <thead>
      <tr>
        <th>No</th>
        <th>공지유형</th>
        <th>공지제목</th>
        <th>작성자</th>
        <th>게시일자</th>
        <th>만료일자</th>
        <th>알림</th>
        <th>수정보기</th>
      </tr>
    </thead>
    <tbody>
      <tr class="hi" v-for="(row, i) in list" :key="i">
        <a v-on:click="fnView(`${row.idx}`)">{{ row.No }}</a>
        <td>{{ row.notiType }}</td>
        <td>{{ row.notiTitle }}</td>
        <td>{{ row.notiOwner }}</td>
        <td>{{ row.startDate }}</td>
        <td>{{ row.endDate }}</td>
        <td>{{ row.sendResult }}</td>
        <td>
          <div class="table-button-container">
            <button
              class="w3-button w3-round w3-green"
              v-on:click="fnView(`${row.idx}`)"
            >
              <i class="fa fa-remove"></i>상세</button
            >&nbsp;&nbsp;
          </div>
        </td>
      </tr>
    </tbody>
  </table>
  <div
    class="pagination w3-bar w3-padding-16 w3-small"
    v-if="paging.totalCount > 0"
  >
    <span class="pg">
      <a
        href="javascript:;"
        @click="fnPage(1)"
        class="first w3-button w3-bar-item w3-border"
        >&lt;&lt;</a
      >
      <a
        href="javascript:;"
        v-if="paging.start_page > 10"
        @click="fnPage(`${paging.start_page - 1}`)"
        class="prev w3-button w3-bar-item w3-border"
        >&lt;</a
      >
      <template v-for="(n, index) in paginavigation()">
        <template v-if="paging.page == n">
          <strong
            class="w3-button w3-bar-item w3-border w3-green"
            :key="index"
            >{{ n }}</strong
          >
        </template>
        <template v-else>
          <a
            class="w3-button w3-bar-item w3-border"
            href="javascript:;"
            @click="fnPage(`${n}`)"
            :key="index"
            >{{ n }}</a
          >
        </template>
      </template>
      <a
        href="javascript:;"
        v-if="paging.total_page > paging.end_page"
        @click="fnPage(`${paging.end_page + 1}`)"
        class="next w3-button w3-bar-item w3-border"
        >&gt;</a
      >
      <a
        href="javascript:;"
        @click="fnPage(`${paging.total_page}`)"
        class="last w3-button w3-bar-item w3-border"
        >&gt;&gt;</a
      >
    </span>
  </div>
</template>

<script>
export default {
  data() {
    //변수생성
    return {
      selected: [],
      selectAll: false,
      requestBody: {}, //리스트 페이지 데이터전송
      list: {}, //리스트 데이터
      no: "", //게시판 숫자처리
      paging: {
        totalCount: 0,
        total_page: 0,
        page: 0,
        start_page: 0,
        end_page: 0,
        ipp: 0,
      }, //페이징 데이터
      page: this.$route.query.page ? this.$route.query.page : 1,
      size: this.$route.query.size ? this.$route.query.size : 10,
      startDate: this.$route.query.startDate,
      endDate: this.$route.query.endDate,
      notiType: this.$route.query.notiType,
      notiContent: this.$route.query.notiContent,
      sendResult: this.$route.query.sendResult,

      paginavigation: function () {
        //페이징 처리 for문 커스텀
        let pageNumber = []; //;
        let start_page = this.paging.start_page;
        let end_page = this.paging.end_page;
        console.log("start_page: %d", start_page);
        console.log("end_page: %d", end_page);

        for (let i = start_page; i <= end_page; i++) {
          pageNumber.push(i);
        }
        return pageNumber;
      },
    };
  },
  mounted() {
    this.fnGetList();
  },
  methods: {
    select() {
      this.selected = [];
      if (!this.selectAll) {
        for (let i in this.list) {
          this.selected.push(this.list[i].idx);
        }
      }
    },
    fnGetList() {
      this.requestBody = {
        // 데이터 전송
        page: this.page,
        size: this.size,
        startDate: this.startDate,
        endDate: this.endDate,
        notiType: this.notiType,
        notiContent: this.notiContent,
        sendResult: this.sendResult,
      };
      this.axios
        .get(this.$serverUrl + "/notice/getNoticeList", {
          params: this.requestBody,
          headers: {},
        })
        .then((res) => {
          if (res.data.resultCode == "00") {
            this.list = res.data.list;
            this.paging = res.data.paging;
            console.log("-----");
            this.no =
              this.paging.totalCount - (this.paging.page - 1) * this.paging.ipp;
          } else {
            alert("검색에 실패했습니다.(에러: " + res.data.resultCode + ")");
          }
        })
        .catch((err) => {
          if (err.message.indexOf("Network Error") > -1) {
            alert("네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요.");
          } else {
            alert(err.message);
          }
        });
    },
    fnSearch() {
      //검색
      this.paging.page = 1;
      this.page = 1;
      console.log(this.paging.page);
      console.log(this.page);

      if (isNaN(this.size)) {
        //alert("검색단위를 숫자로 입력해 주세요.");
        //return;
        this.size = 10;
        this.$refs.sizeInput = this.size;
      }
      this.fnGetList();
    },
    fnPage(n) {
      //console.log(n);
      if (this.page !== n) {
        this.page = n;
        this.fnGetList();
      }
    },
    fnList() {
      this.page = 1;
      this.size = 10;
      this.startDate = "";
      this.endDate = "";
      this.notiType = "";
      this.sendResult = "";
      this.fnGetList();
    },
    fnView(idx) {
      this.requestBody.idx = idx;
      this.$router.push({
        path: "./detail",
        query: this.requestBody,
      });
    },
    fnWrite() {
      this.$router.push({
        path: "./insert",
      });
    },
    fnDelete() {
      var result = confirm("삭제하시겠습니까?");
      console.log("this.selected.length: " + this.selected.length);
      // let selectedItems = this.selected.length;
      for (let i = 0; i < this.selected.length; ++i) {
        // this.selected.push(this.list[i].idx);
        if (result) {
          this.axios
            .delete(
              this.$serverUrl + "/notice/deleteNotice/" + this.list[i].idx,
              {}
            )
            .then((res) => {
              console.log("res.data.resultCode: " + res.data.resultCode);
              if (res.data.resultCode == "00") {
                alert("삭제되었습니다.");
                //alert(JSON.stringify(res.data.resultMsg));
                this.fnList();
              } else {
                alert(
                  `월패드알림 Y 이므로 삭제 불가: ${this.list[i].notiTitle}`
                );
              }
            })
            .catch((err) => {
              console.log(err);
            });
        }
      }
    },
  },
};
</script>

<style scoped>
.hi:hover {
  background-color: yellow;
}
body {
  padding: 50px;
}
</style>
