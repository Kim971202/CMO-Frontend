<template>
  <div class="board">
    <h2>관리비 상세 내역</h2>
    <table>
      <colgroup>
        <col style="width: 18.5%" />
        <col style="width: " />
      </colgroup>
      <tbody>
        <tr>
          <th scope="row">고지서 날짜</th>
          <td>{{ mngYear }}년 {{mngMonth}}월</td>
        </tr>
        <tr>
          <th scope="row">동</th>
          <td>{{ dongCode }}</td>
          <th scope="row">호</th>
          <td>{{ hoCode }}</td>
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
      dongCode: "",
      hoCode: "",
      mngYear: "",
      mngMonth: "",

    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    fnGetView() {
      this.axios
        .get(this.$serverUrl + "/mngFee/getDetailedMngFee/" ,{
          params: this.requestBody,
        })
        .then((res) => {
          this.dongCode = res.data.dongCode;
          this.hoCode = res.data.hoCode;
          this.mngYear = res.data.mngYear;
          this.mngMonth = res.data.mngMonth;
        })
        .catch((err) => {
          if (err.message.indexOf("Network Error") > -1) {
            alert("네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요.");
          }
        });
        console.log(this.axios
        .get(this.$serverUrl + "/mngFee/getDetailedMngFee/" ,{
          params: this.requestBody,
        }))
    },
    fnList() {
      delete this.requestBody.idx;
      this.$router.push({
        path: "./list",
        query: this.requestBody,
      });
    },
  },
};
</script>
<style scoped></style>
