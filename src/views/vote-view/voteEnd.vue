<template>
  <div class="board">
    <h2>주민투표 마감 / 오프라인 투표함 개표결과</h2>
    <table>
      <colgroup>
        <col style="width: 10%" />
        <col style="width: 70%" />
        <col style="width: 50%" />
      </colgroup>
      <tbody>
        <tr>
          <th scope="row">항목</th>
          <table>
            <tr v-for="(item, index) in voteItems" :key="index">
              <td>
                <input
                  v-model="voteNumberOff[index]"
                  type="number"
                  min="0"
                  :key="index"
                />
              </td>
              <td>{{ item.itemNo }}</td>
              <td>{{ item.itemContent }}</td>
            </tr>
          </table>
        </tr>
      </tbody>
    </table>

    <div class="common-buttons">
      <button
        type="button"
        class="w3-button w3-round w3-blue"
        v-on:click="fnVoteEnd"
      >
        투표마감하기</button
      >&nbsp; &nbsp;
      <button
        type="button"
        class="w3-button w3-round w3-black"
        v-on:click="fnList"
      >
        취소</button
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
      vStartDTime: "",
      vEndDTime: "",
      voteItems: [],
      voteNumberOff: [],
    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    fnGetView() {
      this.axios
        .get(this.$serverUrl + "/vote/getDetailedVoteAgenda/", {
          params: this.requestBody,
        })
        .then((res) => {
          this.voteTitle = res.data.voteTitle;
          this.vStartDTime = res.data.vStartDTime;
          this.vEndDTime = res.data.vEndDTime;
          this.voteItems = res.data.voteItems;
        })
        .catch((err) => {
          if (err.message.indexOf("Network Error") > -1) {
            alert("네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요.");
          }
        });
    },
    fnVoteEnd() {
      let apiUrl = this.$serverUrl + "/vote/postOffVote";
      this.form = {
        idx: this.idx,
        voteNumberOff: this.voteNumberOff,
        voteItems: this.voteItems,
      };
      var result = confirm("마감하시겠습니까?");
      if (result) {
        console.log(this.form);
        this.axios
          .post(apiUrl, this.form)
          .then((res) => {
            console.log("res.data.resultCode: " + res.data.resultCode);
            if (res.data.resultCode == "00") {
              //   this.fnList();
            } else {
              alert("마김처리 되지 않았습니다.");
            }
          })
          .catch((err) => {
            if (err.message.indexOf("Network Error") > -1) {
              alert(
                "네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요."
              );
            }
          });
      }
      this.$router.push({
        path: "./voteFn",
        query: this.requestBody,
      });
    },
    fnList() {
      this.$router.push({
        path: "./detail",
        query: this.requestBody,
      });
    },
    fnUpdate() {
      this.$router.push({
        path: "./update",
        query: this.requestBody,
      });
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
