<template>
  <div class="board">
    <h2>주민투표 상세보기</h2>
    <table>
      <colgroup>
        <col style="width: 18.5%" />
        <col style="width: *" />
      </colgroup>
      <tbody>
        <tr>
          <th scope="row">투표제목</th>
          <td class="title">{{ voteTitle }}</td>
          <td></td>
        </tr>
        <tr>
          <th scope="row">투표내용</th>
          <td>{{ voteDesc }}</td>
          <td></td>
        </tr>
        <tr>
          <th scope="row">시작일시</th>
          <td>{{ vStartDTime }}</td>
          <td></td>
        </tr>
        <tr>
          <th scope="row">마감일시</th>
          <td>{{ vEndDTime }}</td>
          <td></td>
        </tr>
      </tbody>
      <tbody>
        <th rowspan="6">항목</th>
        <tr v-for="(item, index) in voteItems" v-bind:key="index">
          <td>{{ item.itemNo }}</td>
          <td>{{ item.itemContent }}</td>
        </tr>
        <colgroup>
          <col style="width: 15%" />
          <col style="width: 15%" />
          <col style="width: *" />
          <col style="width: *" />
        </colgroup>
      </tbody>
    </table>

    <div class="common-buttons">
      <button
        type="button"
        class="w3-button w3-round w3-blue-gray"
        v-on:click="fnUpdate"
      >
        수정</button
      >&nbsp;
      <button
        type="button"
        class="w3-button w3-round w3-red"
        v-on:click="fnDelete"
      >
        삭제</button
      >&nbsp;
      <button
        type="button"
        class="w3-button w3-round w3-blue"
        v-on:click="fnVoteEnd"
      >
        투표마감</button
      >&nbsp;
      <button
        type="button"
        class="w3-button w3-round w3-green"
        v-on:click="fnvoteResult"
      >
        결과보기
      </button>
      &nbsp;
      <button
        type="button"
        class="w3-button w3-round w3-black"
        v-on:click="fnList"
      >
        종료</button
      >&nbsp;
    </div>
  </div>
</template>

<script>
export default {
  data() {
    //변수생성
    return {
      requestBody: this.$route.query,
      idx: this.$route.query.idx,
      voteTitle: "",
      voteDesc: "",
      vStartDTime: "",
      vEndDTime: "",
      voteItems: [],
    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    fnGetDate() {
      let checkDate = new Date();
      checkDate.toISOString().split("T")[0];
      const offset = checkDate.getTimezoneOffset();
      checkDate = new Date(checkDate.getTime() - offset * 60 * 1000);
      return checkDate.toISOString().split("T")[0];
    },
    fnGetView() {
      this.axios
        .get(this.$serverUrl + "/vote/getDetailedVoteAgenda/", {
          params: this.requestBody,
        })
        .then((res) => {
          this.voteTitle = res.data.voteTitle;
          this.voteDesc = res.data.voteDesc;
          this.vStartDTime = res.data.vStartDTime;
          this.vEndDTime = res.data.vEndDTime;
          this.voteItems = res.data.voteItems;
          this.voteEndFlag = res.data.voteEndFlag;
        })
        .catch((err) => {
          if (err.message.indexOf("Network Error") > -1) {
            alert("네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요.");
          }
        });
    },
    fnvoteResult() {
      if (this.voteEndFlag == "N") {
        alert("종료되지 않은 투표입니다.");
      } else {
        this.$router.push({
          path: "./voteFn",
          query: this.requestBody,
        });
      }
    },
    fnVoteEnd() {
      if (this.voteEndFlag == "Y") {
        alert("이미 종료된 투표 입니다.");
      } else {
        this.$router.push({
          path: "./voteEnd",
          query: this.requestBody,
        });
      }
    },
    fnList() {
      delete this.requestBody.idx;
      this.$router.push({
        path: "./list",
        query: this.requestBody,
      });
    },

    fnUpdate() {
      if (this.vStartDTime > this.fnGetDate()) {
        this.$router.push({
          path: "./update",
          query: this.requestBody,
        });
      } else {
        alert("진행중이거나 종료된 투표는 수정 할수없습니다.");
      }
    },
    fnDelete() {
      if (this.vStartDTime > this.fnGetDate()) {
        var result = confirm("삭제하시겠습니까?");
        if (result) {
          this.axios
            .delete(this.$serverUrl + "/vote/deleteVoteAgenda/" + this.idx, {})
            .then((res) => {
              console.log("res.data.resultCode: " + res.data.resultCode);
              if (res.data.resultCode == "00") {
                alert("삭제되었습니다.");
                this.fnList();
              } else {
                alert("삭제되지 않았습니다.");
              }
            })
            .catch((err) => {
              console.log(err);
            });
        }
      } else {
        alert("진행중이거나 종료된 투표는 삭제 할수없습니다.");
      }
    },
  },
};
</script>
<style scoped>
.board table {
  width: 100%;
  border-top: 2px solid #1d4281;
  border-spacing: 0;
}
</style>
