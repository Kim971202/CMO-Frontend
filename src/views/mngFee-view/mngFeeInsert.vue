<template>
  <meta charset="UTF-8" />
  <div class="board">
    <h1>관리비 일괄 등록</h1>
    <table>
      <colgroup>
        <col style="width: 20%" />
        <col style="width: 80%" />
        <col style="width: 10%" />
        <col style="width: 10%" />
      </colgroup>
      <tbody>
        <tr>
          <th scope="row">고지서 날짜정보</th>
          <td style="float: center">
            <input
              type="month"
              style="width: 200px; text-align: center"
              ref="mngFeeDateInput"
              v-model.trim="mngFeeDate"
            />
          </td>
        </tr>
        <tr>
          <input
            type="file"
            @change="onFileChange"
            charset="UTF-8"
            id="images"
            ref="fileInput"
          />
        </tr>
      </tbody>
    </table>
    <div class="common-buttons">
      <button
        type="button"
        class="w3-button w3-round w3-blue-gray"
        v-on:click="fnSave"
      >
        등록</button
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
      mngFeeDate: "",
      file: "",
      fileType: 4,
    };
  },

  mounted() {},
  methods: {
    onFileChange(event) {
      this.selectedFile = event.target.files[0];
      if (this.selectedFile != null) {
        this.onUpload();
      }
    },
    onUpload() {
      this.requestBody = {
        fileType: this.fileType,
      };
      const fd = new FormData();
      fd.append("file", this.selectedFile, this.selectedFile.name);
      this.axios
        .post(this.$serverUrl + "/fileUpload/file", fd, {
          params: this.requestBody,
          responseType: "blob",
        })
        .then((res) => {
          console.log(res);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    fnSave() {
      if (this.mngFeeDate == "") {
        alert("날짜정보를 입력하세요");
        this.$refs.mngFeeDateInput.focus();
        return;
      } else if (this.images == "") {
        alert("고지서 파일을 등록하세요");
        this.$refs.fileInput.focus();
        return;
      }

      const fd = new FormData();
      fd.append("file", this.selectedFile, this.selectedFile.name);

      let apiUrl = this.$serverUrl + "/mngFee/postMngFee";
      this.form = {
        mngFeeDate: this.mngFeeDate,
      };

      var result = confirm("등록하시겠습니까?");
      if (result) {
        //INSERT
        this.axios
          .post(apiUrl, this.form)
          .then((res) => {
            console.log("res.data.resultCode: " + res.data.resultCode);
            if (res.data.resultCode == "00") {
              this.fnList();
            } else {
              alert("등록되지 않았습니다.");
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
    fnClear() {
      this.dongCode = "";
      this.hoCode = "";
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
<style scoped>
input[type="file"] {
  width: 350px;
  max-width: 100%;
  color: #444;
  padding: 5px;
  background: #fff;
  border-radius: 10px;
  border: 1px solid #555;
}

input[type="file"]::file-selector-button {
  margin-right: 20px;
  border: none;
  background: #084cdf;
  padding: 10px 20px;
  border-radius: 10px;
  color: #fff;
  cursor: pointer;
  transition: background 0.2s ease-in-out;
}

input[type="file"]::file-selector-button:hover {
  background: #0d45a5;
}
</style>
