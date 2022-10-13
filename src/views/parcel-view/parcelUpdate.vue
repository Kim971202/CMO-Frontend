<template>
  <div class="board">
    <h1>This is an board edit page</h1>
    <table>
      <colgroup>
        <col style="width: 18.5%" />
        <col style="width: auto" />
      </colgroup>
      <tbody>
        <tr>
          <th scope="row">도착일시</th>
          <td>{{ arrivalTime }}</td>
        </tr>
        <tr>
          <th scope="row">수령여부</th>
          <td>
            <textarea
              rows="1"
              placeholder="미수령 OR 수령 OR 반품"
              ref="parcelStatusTextArea"
              v-model.trim="parcelStatus"
            ></textarea>
          </td>
        </tr>
        <tr>
          <th scope="row">동</th>
          <td>{{ dongCode }}</td>
        </tr>
        <tr>
          <th scope="row">호</th>
          <td>{{ hoCode }}</td>
        </tr>
        <tr>
          <th scope="row">메모(택배사)</th>
          <td>{{ parcelCorp }}</td>
        </tr>
      </tbody>
    </table>

    <div class="common-buttons">
      <button
        type="button"
        class="w3-button w3-round w3-blue-gray"
        v-on:click="fnSave"
      >
        수정</button
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

      arrivalTime: "",
      parcelFlag: "",
      dongCode: "",
      hoCode: "",
      parcelCorp: "",
    };
  },
  mounted() {
    this.fnGetView();
  },
  methods: {
    fnGetView() {
      if (this.idx !== undefined) {
        this.axios
          .get(this.$serverUrl + "/parcel/getDetailedParcelList/" + this.idx, {
            params: this.requestBody,
          })
          .then((res) => {
            this.arrivalTime = res.data.arrivalTime;
            this.parcelFlag = res.data.parcelFlag;
            this.dongCode = res.data.dongCode;
            this.hoCode = res.data.hoCode;
            this.parcelCorp = res.data.parcelCorp;
          })
          .catch((err) => {
            console.log(err);
          });
      }
    },
    fnList() {
      delete this.requestBody.idx;
      this.$router.push({
        path: "./getParcelList",
        query: this.requestBody,
      });
    },
    fnView(idx) {
      this.requestBody.idx = idx;
      this.$router.push({
        path: "./detail",
        query: this.requestBody,
      });
    },
    fnSave() {
      if (this.contents == "") {
        alert("택배상태를 입력하세요.");
        this.$refs.parcelStatusTextArea.focus();
        return;
      }

      let apiUrl = this.$serverUrl + "/parcel/updateParcel";
      this.form = {
        idx: this.idx,
        parcelStatus: this.parcelStatus,
      };

      var result = confirm("수정하시겠습니까?");
      if (result) {
        //UPDATE
        this.axios
          .put(apiUrl, this.form)
          .then((res) => {
            console.log("res.data.resultCode: " + res.data.resultCode);
            if (res.data.resultCode == "00") {
              alert("수정되었습니다.");
              //alert(JSON.stringify(res.data.resultMsg));
              this.fnView(res.data.idx);
            } else {
              alert("수정되지 않았습니다.");
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
    },
  },
};
</script>
<style scoped></style>
