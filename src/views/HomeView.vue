<template>
  <div
    id="mainTopCarousel"
    class="carousel slide carousel-fade"
    data-bs-ride="carousel"
    data-bs-pause="false"
  >
    <ol class="carousel-indicators">
      <li class="carousel-control" @click="mainTopCarouselPrevClick">
        <i class="bi bi-chevron-left"></i>
      </li>
      <li
        data-bs-target="#mainTopCarousel"
        data-bs-slide-to="0"
        class="active"
        aria-current="true"
        aria-label="Slide 1"
      ></li>
      <li
        data-bs-target="#mainTopCarousel"
        data-bs-slide-to="1"
        aria-current="true"
        aria-label="Slide 1"
      ></li>
      <li
        class="carousel-control"
        @click="mainTopCarouselPlayPauseClick($event)"
      >
        <i class="bi bi-pause-fill"></i>
      </li>
      <li class="carousel-control" @click="mainTopCarouselNextClick">
        <i class="bi bi-chevron-right"></i>
      </li>
    </ol>
    <div class="carousel-inner">
      <div class="carousel-item active">
        <div class="main-top-promotion">
          <video
            class="main-top-promotion-video"
            autoplay="autoplay"
            loop="loop"
            muted="muted"
          >
            <source
              src="../assets/AdobeStock_197561178_AdobeCreativeCloudExpress.mp4"
              type="video/mp4"
            />
          </video>
          <div class="container">
            <div class="row align-items-center">
              <div class="col-sm-12 col-md-6 text-md-start">
                <div class="py-5">
                  <h1 class="text-white fw-bold">
                    Welcome to Your <br />Vue.JS Application
                  </h1>
                  <p class="text-white mb-4 fs-5">
                    당신의 이야기가 세상을 바꿉니다.
                  </p>
                  <router-link
                    :to="{ name: 'BoardWrite' }"
                    class="btn btn-success"
                    >나의 이야기 쓰기</router-link
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="carousel-item">
        <div class="main-top-promotion">
          <video
            class="main-top-promotion-video"
            autoplay="autoplay"
            loop="loop"
            muted="muted"
          >
            <source
              src="../assets/AdobeStock_375502954_AdobeCreativeCloudExpress.mp4"
              type="video/mp4"
            />
          </video>
          <div class="container">
            <div class="row align-items-center">
              <div class="col-sm-12 col-md-6 text-md-start">
                <div class="py-5">
                  <h1 class="text-white fw-bold">
                    반려동물과의 <br />희로애락(喜怒哀樂)
                  </h1>
                  <p class="text-white mb-4 fs-5">
                    나의 반려생활을 공유합니다.
                  </p>
                  <router-link
                    :to="{ name: 'BoardList' }"
                    class="btn btn-primary mx-2"
                    >반려생활 소식</router-link
                  >
                  <router-link
                    :to="{ name: 'BoardWrite' }"
                    class="btn btn-success"
                    >나의 반려생활 쓰기</router-link
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <button
      class="carousel-control-prev"
      type="button"
      data-bs-target="#mainTopCarousel"
      data-bs-slide="prev"
    >
      <span class="carousel-control-prev-icon" aria-hidden="true"></span>
      <span class="visually-hidden">Previous</span>
    </button>
    <button
      class="carousel-control-next"
      type="button"
      data-bs-target="#mainTopCarousel"
      data-bs-slide="next"
    >
      <span class="carousel-control-next-icon" aria-hidden="true"></span>
      <span class="visually-hidden">Next</span>
    </button>
  </div>
  <!-- <div class="py-5">
    <div class="container text-start">
      <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3">
        <div
          class="col"
          v-for="boardItem in boardList"
          v-bind:key="boardItem.no"
        >
          <div class="card">
            <div class="card-body">
              <h5 class="card-title">{{ boardItem.subject }}</h5>
              <h6 class="card-subtitle mb-2 text-muted">
                {{ boardItem.writer }}
              </h6>
              <div class="d-flex justify-content-between align-items-center">
                <div class="btn-group">
                  <button
                    type="button"
                    class="btn btn-sm btn-outline-secondary"
                    @click="boardNoClick(boardItem)"
                  >
                    보기
                  </button>
                </div>
                <small class="text-muted">{{ boardItem.writedate }}</small>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div> -->
  <div class="py-5">
    <div class="container text-start">
      <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3">
        <div
          class="col my-2"
          v-for="boardItem in boardList"
          v-bind:key="boardItem.no"
        >
          <div class="card">
            <img
              class="card-img-top"
              :src="
                (boardItem.poster.toUpperCase().startsWith('HTTP')
                  ? ''
                  : 'http://localhost:9000') + boardItem.poster
              "
              alt=""
            />
            <div class="card-body">
              <h5 class="card-title">{{ boardItem.subject }}</h5>
              <h6 class="card-subtitle mb-2 text-muted">
                {{ boardItem.writer }}
              </h6>
              <p class="card-text">{{ boardItem.content }}</p>
              <div class="d-flex justify-content-between align-items-center">
                <div class="btn-group">
                  <button
                    type="button"
                    class="btn btn-sm btn-outline-secondary"
                    @click="boardNoClick(boardItem)"
                  >
                    보기
                  </button>
                </div>
                <small class="text-muted">{{ boardItem.writedate }}</small>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row my-2 mx-auto">
        <button
          type="button"
          class="btn btn-sm btn-primary"
          id="boardMoreButton"
          @click="boardPaging()"
        >
          더 보기
        </button>
      </div>
    </div>
  </div>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from "@/components/HelloWorld.vue";
import * as bootstrap from "bootstrap";

export default {
  name: "HomeView",
  components: {
    HelloWorld,
  },
  data: function () {
    return {
      boardList: [],
      boardPagination: {},
    };
  },
  methods: {
    mainTopCarouselPrevClick() {
      var mainTopCarousel = document.querySelector("#mainTopCarousel");
      var carousel = bootstrap.Carousel.getInstance(mainTopCarousel);
      carousel.prev();
    },
    mainTopCarouselNextClick() {
      var mainTopCarousel = document.querySelector("#mainTopCarousel");
      var carousel = bootstrap.Carousel.getInstance(mainTopCarousel);
      carousel.next();
    },
    mainTopCarouselPlayPauseClick(event) {
      var mainTopCarousel = document.querySelector("#mainTopCarousel");
      var carousel = bootstrap.Carousel.getInstance(mainTopCarousel);
      if (mainTopCarousel.classList.contains("pause")) {
        mainTopCarousel.classList.remove("pause");
        event.target.classList.remove("bi-play-fill");
        event.target.classList.add("bi-pause-fill");
        carousel.cycle();
      } else {
        mainTopCarousel.classList.add("pause");
        event.target.classList.remove("bi-pause-fill");
        event.target.classList.add("bi-play-fill");
        carousel.pause();
      }
    },
    // getBoardList() {
    //   this.axios
    //     .get("http://localhost:9000/boards")
    //     .then((res) => {
    //       console.log(res);
    //       this.boardList = res.data.data;
    //     })
    //     .catch((err) => {
    //       console.log(err);
    //     });
    // },
    getBoardList(pageNo) {
      this.axios
        .get(
          "http://localhost:9000/boards?countperpage=3&pageno=" +
            pageNo +
            "&sortby=writedate.desc"
        )
        .then((res) => {
          //console.log(res);
          this.boardList = this.boardList.concat(res.data.data);
          this.boardPagination = res.data.pagination;
          if (!this.boardPagination.enableNextPageNo) {
            var button = document.getElementById("boardMoreButton");
            button.disabled = "disabled";
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    boardNoClick(boardItem) {
      this.$router.push({
        name: "BoardView",
        query: { boardNo: boardItem.no },
      });
    },
    boardPaging() {
      if (
        this.boardPagination != undefined &&
        this.boardPagination.enableNextPageNo
      ) {
        this.getBoardList(this.boardPagination.pageNo + 1);
      }
    },
  },
  // mounted() {
  //   this.getBoardList();
  // },
  mounted() {
    this.getBoardList(1);
  },
  computed: {
    pagingInfo() {
      var pagingInfoText = "0 / 0";
      if (
        this.boardPagination != undefined &&
        this.boardPagination.pageNo != undefined &&
        this.boardPagination.lastPageNo != undefined
      ) {
        pagingInfoText =
          this.boardPagination.pageNo + " / " + this.boardPagination.lastPageNo;
      }
      return pagingInfoText;
    },
  },
};
</script>

<style>
.main-top-promotion {
  position: relative;
  width: 100%;
}

.main-top-promotion-video {
  height: 100%;
  width: 100%;
  margin: 0px;
  padding: 0px;
  position: absolute;
  left: 0;
  top: 0;
  object-fit: cover;
}

.main-top-promotion > .container {
  position: relative;
}

.carousel-control-next,
.carousel-control-prev {
  width: 7% !important;
}

.main-top-crousel-startpause {
  border: 0;
  background-color: transparent;
  color: white;
}

.carousel-indicators > li {
  height: 10px !important;
  width: 10px !important;
  border-radius: 50%;
  margin-right: 10px !important;
}

.carousel-indicators > li.carousel-control {
  line-height: 30px;
  color: #fff;
  cursor: pointer;
}

.card-img-top {
  height: 15em;
  object-fit: fill;
}

.card-title {
  overflow: hidden;
  text-overflow: ellipsis;
  line-height: 1.4em;
  height: 2.8em;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

.card-text {
  overflow: hidden;
  text-overflow: ellipsis;
  line-height: 1.4em;
  height: 4.2em;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
}
</style>
