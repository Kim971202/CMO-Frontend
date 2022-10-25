<template>
  <div class="board">
    <h2>에너지 관리</h2>
    <div class="common-buttons"></div>
    <table>
      <colgroup>
        <col style="width: 10%" />
        <col style="width: 10%" />
        <col style="width: 10%" />
        <col style="width: 10%" />
        <col style="width: *" />
        <col style="width: *" />
      </colgroup>

      <tbody>
        <tr>
          <th scope="row">기간별조회</th>
          <td>
            <input type="text" ref="titleInput" v-model.trim="startDate" />
          </td>
          <th scope="row">에너지유형</th>
          <td>
            <input type="text" ref="authorInput" v-model.trim="energyType" />
          </td>
          <th scope="row">시작일자</th>
          <td>
            <input type="text" ref="authorInput" v-model.trim="startDate" />
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
      <button type="button" @click="fnDelete">삭제</button>
    </div>
  </div>
  <div class="text-uppercase text-bold">id selected: {{ selected }}</div>
  <table class="w3-table-all">
    <colgroup>
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
        <label class="form-checkbox">
          <input type="checkbox" v-model="selectAll" @click="select" />
          <i class="form-icon"></i>
        </label>
        <th>No</th>
        <th>월</th>
        <th>세대</th>
        <th>
          전기
          <tr>
            <th>검침</th>
            <th>사용</th>
          </tr>
        </th>
        <th>
          수도
          <tr>
            <th>검침</th>
            <th>사용</th>
          </tr>
        </th>
        <th>
          가스
          <tr>
            <th>검침</th>
            <th>사용</th>
          </tr>
        </th>
        <th>
          온수
          <tr>
            <th>검침</th>
            <th>사용</th>
          </tr>
        </th>
        <th>
          난방
          <tr>
            <th>검침</th>
            <th>사용</th>
          </tr>
        </th>
        <th>
          기타
          <tr>
            <th>검침</th>
            <th>사용</th>
          </tr>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr class="hi" v-for="(row, i) in list" :key="i">
        <td>
          <label class="form-checkbox">
            <input type="checkbox" :value="row.idx" v-model="selected" />
            <i class="form-icon"></i>
          </label>
        </td>
        <a v-on:click="fnView(`${row.date}`)">{{ row.No }}</a>
        
        <td>{{ row.month }}</td>
        <td>{{ row.dongHo }}</td>
        <th>
            <td>{{ row.elecMeter }}</td>
            <td>{{ row.elecUsage }}</td>
        </th>
        <th>
            <td>{{ row.waterMeter }}</td>
            <td>{{ row.waterUsage }}</td>
        </th>
        <th>
            <td>{{ row.gasMeter }}</td>
            <td>{{ row.gasUsage }}</td>
        </th>
        <th>
            <td>{{ row.hotWaterMeter }}</td>
            <td>{{ row.hotWaterUsage }}</td>
        </th>
        <th>
            <td>{{ row.heatingMeter }}</td>
            <td>{{ row.heatingUsage }}</td>
        </th>
        <th>
            <td> 0.00 </td>
            <td> 0.00 </td>
        </th>

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
      dongCode: this.$route.query.dongCode,
      hoCode: this.$route.query.hoCode,
      energyType: this.$route.query.energyType,

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
        dongCode: this.dongCode,
        hoCode: this.hoCode,
        energyType: this.energyType,
      };
      this.axios
        .get(this.$serverUrl + "/ems/getEMS", {
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
      this.fnGetList();
    },
    fnView(date) {
      this.requestBody.date = date;
      this.$router.push({
        path: "./detail",
        query: this.requestBody,
      });
    },

    //TODO: 삭제후 페이지 refresh 필요
    fnDelete() {
      var result = confirm("삭제하시겠습니까?");
      console.log("this.selected.length: " + this.selected.length);
      // let selectedItems = this.selected.length;
      for (let i = 0; i < this.selected.length; ++i) {
        // this.selected.push(this.list[i].idx);
        if (result) {
          this.axios
            .delete(
              this.$serverUrl +
                "/complaint/deleteApplication/" +
                this.list[i].idx,
              {}
            )
            .then((res) => {
              console.log("res.data.resultCode: " + res.data.resultCode);
              if (res.data.resultCode == "00") {
                alert("삭제되었습니다.");
                //alert(JSON.stringify(res.data.resultMsg));
                this.fnList();
              } else {
                alert("삭제되지 않았습니다.");
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
