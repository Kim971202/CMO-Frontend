<template>
  <div class="board">
    <h2>택배</h2>
    <div class="common-buttons"></div>
    <button
      type="button"
      class="w3-button w3-round w3-teal"
      v-on:click="fnWrite"
    >
      신규
    </button>
    <table>
      <colgroup>
        <col style="width: 15%" />
        <col style="width: *" />
        <col style="width: 15%" />
        <col style="width: 15%" />
        <col style="width: 15%" />
        <col style="width: 15%" />
      </colgroup>

      <tbody>
        <tr>
          <th scope="row">시작일자</th>
          <td>
            <input type="text" ref="titleInput" v-model.trim="startDate" />
          </td>
          <th scope="row">종료일자</th>
          <td>
            <input type="text" ref="authorInput" v-model.trim="endDate" />
          </td>
          <th scope="row">동 / 호</th>
          <td>
            <input
              type="text"
              ref="authorInput"
              v-model.trim="dongCode"
              placeholder="동"
            />
            <input
              type="text"
              ref="authorInput"
              v-model.trim="hoCode"
              placeholder="호"
            />
          </td>
          <th scope="row">수령여부</th>
          <td>
            <input type="text" ref="titleInput" v-model.trim="startDate" />
          </td>
          <th scope="row">통신결과</th>
          <td>
            <input type="text" ref="titleInput" v-model.trim="startDate" />
          </td>
        </tr>
      </tbody>
      <tbody>
        <tr>
          <th scope="row">검색단위</th>
          <td colspan="2">
            <input
              type="text"
              ref="sizeInput"
              v-model="size"
              @keyup.enter="fnSearch"
            />
          </td>
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
  <table></table>
  <table class="w3-table-all">
    <colgroup>
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: 10%" />
      <col style="width: 10%" />
    </colgroup>
    <thead>
      <tr>
        <th>
          <input type="checkbox" value="all" v-model="allSelected" />
        </th>
        <th>No</th>
        <th>동</th>
        <th>호</th>
        <th>구분</th>
        <th>택배회사</th>
        <th>도착일시</th>
        <th>수령 일시</th>
        <th>통신결과</th>
      </tr>
    </thead>
    <tbody>
      <input
        type="checkbox"
        :id="row"
        :value="row"
        v-model="selectList"
        :key="i"
      />
      <label :for="row" :key="i + '1'"> {{ i }}</label>
      <span>check: {{ selectList }}</span>

      <tr v-for="(row, i) in list" :key="i">
        <input type="checkbox" value="all" v-model="allSelected" />
        <label for="all">전체</label>
        <td>
          <a v-on:click="fnView(`${row.idx}`)">{{ row.No }}</a>
        </td>
        <td>{{ row.dongCode }}</td>
        <td>{{ row.hoCode }}</td>
        <td>{{ row.parcelFlag }}</td>
        <td>{{ row.parcelCorp }}</td>
        <td>{{ row.arrivalTime }}</td>
        <td>{{ row.receiveTime }}</td>
        <td>{{ row.sendResult }}</td>
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
      dongCode: this.$route.query.dongCode,
      hoCode: this.$route.query.hoCode,
      parcelStatus: this.$route.query.parcelStatus,
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
      checkList: ["e-mail", "SMS", "test1"],
      selectList: [],
      computed: {
        allSelected: {
          //getter
          get: function () {
            return this.checkList.length === this.selectList.length;
          },
          //setter
          set: function (e) {
            this.selectList = e ? this.checkList : [];
          },
        },
      },
    };
  },
  mounted() {
    this.fnGetList();
  },
  methods: {
    fnGetList() {
      this.requestBody = {
        // 데이터 전송
        page: this.page,
        size: this.size,
        startDate: this.startDate,
        endDate: this.endDate,
        dongCode: this.dongCode,
        hoCode: this.hoCode,
        parcelStatus: this.parcelStatus,
        sendResult: this.sendResult,
      };
      this.axios
        .get(this.$serverUrl + "/parcel/getParcelList", {
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
      this.fnGetList();
    },
  },
};
</script>

<style scoped></style>
