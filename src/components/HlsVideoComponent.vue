<template>
  <div class="cnt video_cnt">
    <div class="video_container">
      <div class="liveInd">
        <span :class="[{ off: !isLive }, 'live']"></span>
      </div>

      <transition-group name="fade" tag="div" class="transitionGroup">
        <div class="videoWrapper" v-show="loading" :key="1">
          <div class="videoSpinner">
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
          </div>
        </div>

        <div v-show="!loading" :key="2" class="videoWrapper">
          <div
            v-if="hlsSource !== ''"
            ref="muteBtnRef"
            :class="[{ on: isMute }, 'muteBtn']"
          >
            Mute
          </div>
          <video ref="videoRef" width="100%"></video>
        </div>
      </transition-group>
    </div>

    <div class="row">
      <button type="button" class="btn" @click="getItemFromList()">
        <span>NEXT CHANEL</span>
      </button>
    </div>
  </div>
</template>
<script>
import Hls from "hls.js";

const M3U8_LIST = [
  {
    source: "https://ap02.iqplay.tv:8082/iqb8002/3m9n/playlist.m3u8",
  },
  {
    source: "https://supertennix-l3-live1.secure.footprint.net/restreamer/supertennix_client/gpu-a-c0-16/restreamer/rtmp/hls/h24_supertennix/manifest.m3u8"
  },
];

export default {
  name: "HlsVideoComponent",
  data: function () {
    return {
      hlsSource: "",
      loading: false,
      name: "",
      isMute: false,
      isLive: false,
    };
  },
  mounted() {
    this.getItemFromList();
  },
  methods: {
    async getItemFromList() {
      if (this.$refs.videoRef) {
        this.$refs.videoRef.src = "";
      }
      this.loading = true;
      this.hlsSource = "";
      this.isLive = false;
      // Get random item from M3U8_LIST array
      const randomItem =
        M3U8_LIST[Math.floor(Math.random() * M3U8_LIST.length)];

      const hlsVideoHandler = (time, dataSource) => {
        setTimeout(() => {
          this.hlsSource = dataSource.source;
          console.log(dataSource.source);
          this.loading = false;
          this.isLive = true;

          let video = this.$refs.videoRef;
          let _self = this;

          if (Hls.isSupported()) {
            let hls = new Hls();
            hls.loadSource(`${this.hlsSource}`);
            hls.attachMedia(video);
            hls.on(Hls.Events.MEDIA_ATTACHED, function () {
              let muteBtn = _self.$refs.muteBtnRef;
              if (navigator.userAgent.indexOf("Firefox") != -1) {
                video.muted = true;
                _self.isMute = true;

                video.play();
              } else {
                video.muted = true;
                _self.isMute = true;

                video.play();
                video.muted = false;
                _self.isMute = false;
              }

              muteBtn.addEventListener("click", () => {
                if (video.muted === false) {
                  video.muted = true;
                  _self.isMute = true;
                } else {
                  video.muted = false;
                  _self.isMute = false;
                }
              });
            });
          } else if (video.canPlayType("application/vnd.apple.mpegurl")) {
            video.src = `${this.hlsSource}`;
            video.addEventListener("canplay", function () {
              let muteBtn = _self.$refs.muteBtnRef;
              if (navigator.userAgent.indexOf("Firefox") != -1) {
                video.muted = true;
                _self.isMute = true;

                video.play();
              } else {
                video.muted = true;
                _self.isMute = true;

                video.play();
                video.muted = false;
                _self.isMute = false;
              }

              muteBtn.addEventListener("click", () => {
                if (video.muted === false) {
                  video.muted = true;
                  _self.isMute = true;
                } else {
                  video.muted = false;
                  _self.isMute = false;
                }
              });
            });
          }
        }, time);
      };

      await hlsVideoHandler(3000, randomItem);
    },
  },
};
</script>

<style lang="scss">
.video_cnt {
  width: 100%;
  max-width: 768px;
  margin: auto;

  .video_container {
    position: relative;
    overflow: hidden;
    box-shadow: 0px 30px 92px rgba(0, 114, 195, 0.343477),
      0px 4px 6px rgba(0, 42, 78, 0.138849);
    border-radius: 20px;

    @media screen and(max-width: 736px) {
      border-radius: 5px;
    }

    .videoWrapper {
      position: relative;
      padding-bottom: 56.25%;
      height: 0;
      display: flex;
      justify-content: center;
      align-items: center;

      video {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
      }
    }

    > .transitionGroup {
      z-index: 10;
      position: relative;
      padding-bottom: 56.25%;
      height: 0;
    }

    .muteBtn {
      cursor: pointer;
      position: absolute;
      right: 10px;
      top: 10px;
      z-index: 10;
      color: #ffffff;
      background-color: rgba(#333, 0.7);
      border-radius: 5px;
      display: flex;
      align-items: center;
      justify-content: center;
      width: 55px;
      height: 25px;
      font-size: 16px;
      font-weight: 700;
      border: 1px solid #fff;

      @media screen and(max-width: 736px) {
        font-size: 12px;
        top: 2px;
        right: 2px;
        width: 35px;
        height: 20px;
      }

      &.on {
        &:after {
          display: block;
          content: "";
          width: 45px;
          height: 1px;
          background-color: #fff;
          position: absolute;
          left: 5px;
          top: 12px;
          transform: rotate(20deg);
          z-index: 15;

          @media screen and(max-width: 736px) {
            left: 3px;
            width: 30px;
            top: 8px;
          }
        }
      }
    }

    .liveInd {
      position: absolute;
      left: 10px;
      top: 10px;
      z-index: 20;
      background-color: rgba(#333, 0.6);
      height: 20px;
      font-size: 10px;
      font-weight: 700;
      border-radius: 15px;
      width: 20px;
      display: flex;
      justify-content: center;
      align-items: center;
      line-height: 20px;
      color: #fff;
      text-transform: uppercase;

      @media screen and(max-width: 736px) {
        top: 2px;
        left: 2px;
        width: 15px;
        height: 15px;
      }

      span {
        display: block;
      }

      .live {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background-color: #74A246;

        @media screen and(max-width: 736px) {
          width: 6px;
          height: 6px;
        }

        &.off {
          background-color: #dd0000;
          animation-name: blinker;
          animation-iteration-count: infinite;
          animation-timing-function: cubic-bezier(1, 0, 0, 1);
          animation-duration: 1s;
        }
      }
    }
  }

  .btn {
    outline: 0;
    border: 0;
    min-height: 40px;
    font-size: 18px;
    padding: 5px 15px;
    background: #71A72C;
    border-radius: 3px;
    color: #fff;
    margin-left: 20px;
  }

  > .row {
    margin: 10px 0;
    width: 100%;
    display: flex;
    justify-content: flex-end;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

@keyframes blinker {
  from {
    opacity: 1;
  }
  to {
    opacity: 0.2;
  }
}

/* LOADER */
.videoSpinner {
  color: blue;
  //display: inline-block;
  position: absolute;
  width: 80px;
  height: 80px;
  top: 50%;
  left: 50%;
  transform: translateX(-50%) translateY(-50%);
  div {
    transform-origin: 40px 40px;
    animation: lds-spinner 1.2s linear infinite;

    &:after {
      content: " ";
      display: block;
      position: absolute;
      top: 3px;
      left: 37px;
      width: 7px;
      height: 20px;
      border-radius: 20%;
      background: #FF4E00;
    }
  }

  div:nth-child(1) {
    transform: rotate(0deg);
    animation-delay: -1.1s;
  }

  div:nth-child(2) {
    transform: rotate(30deg);
    animation-delay: -1s;
  }

  div:nth-child(3) {
    transform: rotate(60deg);
    animation-delay: -0.9s;
  }

  div:nth-child(4) {
    transform: rotate(90deg);
    animation-delay: -0.8s;
  }

  div:nth-child(5) {
    transform: rotate(120deg);
    animation-delay: -0.7s;
  }

  div:nth-child(6) {
    transform: rotate(150deg);
    animation-delay: -0.6s;
  }

  div:nth-child(7) {
    transform: rotate(180deg);
    animation-delay: -0.5s;
  }

  div:nth-child(8) {
    transform: rotate(210deg);
    animation-delay: -0.4s;
  }

  div:nth-child(9) {
    transform: rotate(240deg);
    animation-delay: -0.3s;
  }

  div:nth-child(10) {
    transform: rotate(270deg);
    animation-delay: -0.2s;
  }

  div:nth-child(11) {
    transform: rotate(300deg);
    animation-delay: -0.1s;
  }

  div:nth-child(12) {
    transform: rotate(330deg);
    animation-delay: 0s;
  }
}

@keyframes lds-spinner {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
</style>