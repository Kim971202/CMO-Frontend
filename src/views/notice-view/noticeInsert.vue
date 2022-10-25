<template>
    <div class="board">
      <h1>공지사항 업로드</h1>
      <table>
        <colgroup>
          <col style="width: 10%" />
          <col style="width: auto" />
        
        </colgroup>
        <tbody>
        <tr>
          <th scope="row">제목</th>
          <td>
              <input
                  type="text"
                  placeholder="택배함번호 입력"
                  ref="parcelBoxNoInput"
                  v-model.trim="parcelBoxNo"
                  />
            </td>
            </tr>
            <tr>
              <th scope="row">공지기간</th>
              <td>
              <input
                  type="text"
                  placeholder="보관함번호 입력"
                  ref="mailBoxNoInput"
                  v-model.trim="mailBoxNo"
                  />
                  <input
                  type="text"
                  placeholder="보관함번호 입력"
                  ref="mailBoxNoInput"
                  v-model.trim="mailBoxNo"
                  />
            </td>
            </tr>   
         
              <td>
              <th scope="row">월패드 알림</th>
              <input
                  type="text"
                  placeholder="수신자 입력"
                  ref="receiverInput"
                  v-model.trim="receiver"
                  />
            </td>
          <tr>
              <td>
              <th scope="row">만료일자</th>
              <input
                  type="text"
                  placeholder="택배비 입력"
                  ref="delFeeInput"
                  v-model.trim="delFee"
                  />
            </td>
          </tr>
          <tr>
            <th scope="row">UserID</th>
            <td>{{ UserID }}</td>
          </tr>
          
          <tr>
            <th scope="row">메모</th>
            <td>
              <textarea
                rows="1"
                placeholder="미수령 OR 수령 OR 반품"
                ref="parcelCorpTextArea"
                v-model.trim="parcelCorp"
              ></textarea>
            </td>
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
        parcelStatus: "",
        dongCode: "",
        hoCode: "",
        parcelCorp: "",
        parcelBoxNo: "",
        mailBoxNo: "",
        receiver: "",
        delFee: "",
        UserID: this.$store.state.loginStore.memberId,
      };
    },
  
    methods: {
      fnList() {
        delete this.requestBody.idx;
        this.$router.push({
          path: "./list",
          query: this.requestBody,
        });
      },
      fnSave() {
        if (this.title == "") {
          alert("수령여부 입력 필요");
          this.$refs.parcelStatusInput.focus();
          return;
        } else if (this.contents == "") {
          alert("택배 회사를 입력 하세요.");
          this.$refs.parcelCorpTextArea.focus();
          return;
        }
  
        let apiUrl = this.$serverUrl + "/parcel/postParcel";
        this.form = {
          parcelStatus: this.parcelStatus,
          dongCode: this.dongCode,
          hoCode: this.hoCode,
          parcelCorp: this.parcelCorp,
          parcelBoxNo: this.parcelBoxNo,
          mailBoxNo: this.mailBoxNo,
          receiver: this.receiver,
          delFee: this.delFee,
          UserID: this.UserID,
  
        };
  
        var result = confirm("등록하시겠습니까?");
  
        if (result) {
          //INSERT
          this.axios
            .post(apiUrl, this.form)
            .then((res) => {
              console.log("res.data.resultCode: " + res.data.resultCode);
              if (res.data.resultCode == "00") {
                //alert("글이 등록되었습니다.");
                //alert(JSON.stringify(res.data.resultMsg));
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
    },
  };
  </script>
  <style scoped></style>
  