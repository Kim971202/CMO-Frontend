<template>
  <div class="board">
    <h2>민원 상세 조회</h2>
    <table>
      <colgroup>
        <col style="width: 18.5%" />
        <col style="width: " />
      </colgroup>
      <tbody>
        <tr>
          <th scope="row">신청일자</th>
          <td class="title">{{ appDate }}</td>
        </tr>
        <tr>
          <th scope="row">민원유형</th>
          <td>{{ appCode }}</td>
        </tr>
        <tr>
          <th scope="row">민원내용</th>
          <td>{{ appContent }}</td>
        </tr>
        <tr>
          <th scope="row">신청자</th>
          <td>{{ applicant }}</td>
        </tr>
        <tr>
          <th scope="row">신청방법</th>
          <td>{{ appMethod }}</td>
        </tr>
        <tr>
          <th scope="row">접수일자</th>
          <td>{{ appReceiptDate }}</td>
        </tr>
        <tr>
          <th scope="row">처리일자</th>
          <td>{{ appCompleteDate }}</td>
        </tr>
        <tr>
          <th scope="row">진행상태</th>
          <td>{{ progressStatus }}</td>
        </tr>
        <tr>
          <th scope="row">메모</th>
          <td>{{ memo }}</td>
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
        class="w3-button w3-round w3-gray"
        v-on:click="fnList"
      >
        목록
      </button>
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
      appDate: "",
      appCode: "",
      appContent: "",
      applicant: "",
      appMethod: "",
      appReceiptDate: "",
      appCompleteDate: "",
      progressStatus: "",
      memo: "",
    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    fnGetView() {
      this.axios
        .get(
          this.$serverUrl + "/complaint/getDetailedApplicationList/" + this.idx,
          {
            params: this.requestBody,
          }
        )
        .then((res) => {
          this.appDate = res.data.appDate;
          this.appCode = res.data.appCode;
          this.appContent = res.data.appContent;
          this.applicant = res.data.applicant;
          this.appMethod = res.data.appMethod;
          this.appReceiptDate = res.data.appReceiptDate;
          this.appCompleteDate = res.data.appCompleteDate;
          this.progressStatus = res.data.progressStatus;
          this.memo = res.data.memo;
        })
        .catch((err) => {
          if (err.message.indexOf("Network Error") > -1) {
            alert("네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요.");
          }
        });
    },
    fnList() {
      delete this.requestBody.idx;
      this.$router.push({
        path: "./list",
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
          .delete(this.$serverUrl + "/parcel/deleteParcel/" + this.idx, {})
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
    },
  },
};
</script>
<style scoped></style>
