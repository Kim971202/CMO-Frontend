<template>
  <div class="board">
    <h2>주민투표 수정</h2>
    <table>
      <colgroup>
        <col style="width: 18.5%" />
        <col style="width: " />
      </colgroup>
      <tbody>
        <tr>
          <th scope="row">투표제목</th>
          <td colspan="3">
            <input
              type="text"
              rows="1"
              ref="voteTitleInput"
              v-model.trim="voteTitle"
            />
          </td>
        </tr>
        <tr>
          <th scope="row">투표내용</th>
          <td colspan="3">
            <textarea
              rows="2"
              ref="voteDescInput"
              v-model.trim="voteDesc"
            ></textarea>
          </td>
        </tr>
        <tr>
          <th scope="row">투표기간</th>
          <td>
            <input
              type="datetime-local"
              style="width: 200px; text-align: center"
              ref="startDateInput"
              v-model.trim="startDate"
            />
            &nbsp; ~ &nbsp;
            <input
              type="datetime-local"
              style="width: 200px; text-align: center"
              ref="endDateInput"
              v-model.trim="endDate"
            />
          </td>
        </tr>
        <tr>
          <th scope="row">항목</th>
          <table>
            <tr v-for="(item, index) in itemContents" v-bind:key="index">
              <button @click="deleteItem(index)">X</button>
              <input type="text" v-model="item.itemContent" />
            </tr>
          </table>
        </tr>
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
        class="w3-button w3-round w3-black"
        v-on:click="fnView"
      >
        상세보기</button
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
      startDate: "",
      endDate: "",
      itemContents: [],
    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    deleteItem(index) {
      this.itemContents.splice(index, 1);
    },
    fnGetView() {
      this.axios
        .get(this.$serverUrl + "/vote/getDetailedVoteAgenda/", {
          params: this.requestBody,
        })
        .then((res) => {
          this.voteTitle = res.data.voteTitle;
          this.voteDesc = res.data.voteDesc;
          this.startDate = res.data.vStartDTime;
          this.endDate = res.data.vEndDTime;
          this.itemContents = res.data.voteItems;
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
    fnView() {
      this.requestBody.idx;
      this.$router.push({
        path: "./detail",
        query: this.requestBody,
      });
    },

    fnUpdate() {
      let apiUrl = this.$serverUrl + "/vote/updateVoteAgenda";
      this.form = {
        idx: this.idx,
        voteTitle: this.voteTitle,
        startDate: this.startDate.replace("T", " "),
        endDate: this.endDate.replace("T", " "),
        itemContents: this.itemContents,
      };

      var result = confirm("수정하시겠습니까?");
      if (result) {
        this.axios
          .put(apiUrl, this.form)
          .then((res) => {
            console.log("res.data.resultCode: " + res.data.resultCode);
            if (res.data.resultCode == "00") {
              alert("수정되었습니다.");
              //this.fnList();
            } else if (res.data.resultCode == "15") {
              alert("진행또는 완료된 투표는 수정 하실수 없습니다.");
            } else {
              alert("수정되지 않았습니다.");
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
            } else if (res.data.resultCode == "15") {
              alert("진행또는 완료된 투표는 삭제 하실수 없습니다.");
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
