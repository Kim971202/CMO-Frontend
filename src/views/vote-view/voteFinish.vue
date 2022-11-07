<template>
  <div class="board">
    <h2>주민투표 결과보기</h2>
    <table>
      <colgroup>
        <col style="width: 10%" />
        <col style="width: " />
      </colgroup>
      <table>
        <tbody>
          <tr>
            <th>월패드 참여세대</th>
          </tr>
        </tbody>
        <tr v-for="(row, i) in wpVoters" :key="i">
          <td>{{ wpVoters[i] }}</td>
        </tr>
      </table>
      <table>
        <tbody>
          <tr>
            <th>모바일 참여세대</th>
          </tr>
          <tr v-for="(row, i) in mbVoters" :key="i">
            <td>{{ mbVoters[i] }}</td>
          </tr>
        </tbody>
      </table>
      <table>
        <tbody>
          <tr>
            <th>후보 순서</th>
            <th>후보 이름</th>
            <th>총득표수</th>
            <th>투표 결과</th>
          </tr>
          <tr v-for="(row, i) in voteItems" :key="i">
            <td>{{ voteItems[i].itemNo }}</td>
            <td>{{ voteItems[i].itemContent }}</td>
            <td>{{ voteItems[i].votesNumber }}</td>
            <td>{{ voteItems[i].getVotesRate }}</td>
          </tr>
        </tbody>
      </table>
    </table>

    <div class="common-buttons">
      <button
        type="button"
        class="w3-button w3-round w3-blue-gray"
        v-on:click="fnCancelVote"
      >
        투표 마감 취소</button
      >&nbsp;
      <button
        type="button"
        class="w3-button w3-round w3-red"
        v-on:click="fnVoteFinish"
      >
        투표 종료 처리</button
      >&nbsp;
      <button
        type="button"
        class="w3-button w3-round w3-blue"
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
      wpVoters: [],
      mbVoters: [],
      voteItems: [],
    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    fnGetView() {
      this.axios
        .get(this.$serverUrl + "/vote/getVoteResult/", {
          params: this.requestBody,
        })
        .then((res) => {
          this.wpVoters = res.data.results1;
          this.mbVoters = res.data.results2;
          this.voteItems = res.data.voteItems;
        })
        .catch((err) => {
          if (err.message.indexOf("Network Error") > -1) {
            alert("네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요.");
          }
        });
    },
    fnVoteEnd() {
      this.$router.push({
        path: "./voteEnd",
        query: this.requestBody,
      });
    },
    fnList() {
      delete this.requestBody.idx;
      this.$router.push({
        path: "./list",
        query: this.requestBody,
      });
    },
    fnCancelVote() {
      var result = confirm(
        `투표마감을 취소하시면, 등록한 오프라인 득표수는 초기화 됩니다. 그래도 하시겠습니까?`
      );
      if (result) {
        this.$router.push({
          path: "./update",
          query: this.requestBody,
        });
      }
    },
    fnVoteFinish() {
      var result = confirm("투표를 최종마감 하시겠습니까?");
      if (result) {
        this.axios
          .post(this.$serverUrl + "/vote/postEndVote/" + this.idx, {})
          .then((res) => {
            console.log("res.data.resultCode: " + res.data.resultCode);
            if (res.data.resultCode == "00") {
              alert("최종마감 되었습니다.");
              this.fnList();
            } else {
              alert("최종마감 되지않았습니다.");
            }
          })
          .catch((err) => {
            console.log(err);
          });
      }
    },
    fnDelete() {
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
    },
  },
};
</script>
<style scoped></style>
