<template>
  <div
    class="video-player-box"
    :playsinline="playsinline"
    @play="onPlayerPlay($event)"
    @pause="onPlayerPause($event)"
    @ended="onPlayerEnded($event)"
    @loadeddata="onPlayerLoadeddata($event)"
    @waiting="onPlayerWaiting($event)"
    @playing="onPlayerPlaying($event)"
    @timeupdate="onPlayerTimeupdate($event)"
    @canplay="onPlayerCanplay($event)"
    @canplaythrough="onPlayerCanplaythrough($event)"
    @ready="playerReadied"
    @statechanged="playerStateChanged($event)"
    v-video-player:myVideoPlayer="playerOptions"
  ></div>
</template>
<script>
const VueVideoPlayer = require('vue-video-player/dist/ssr');
import Vue from 'vue';
import 'videojs-flash';
import 'video.js/dist/video-js.css'
Vue.use(VueVideoPlayer);
export default {
  props: {
    videoSrc: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      // component options
      playsinline: true,
      // videojs options
      playerOptions: {
        autoplay: true, // 自动播放
        controls: true, // 是否显示控制栏
        language: "zh-CN",
        techOrder: ["flash", "html5","video/mp4"], // 兼容顺序
        flash: { hls: { withCredentials: false } },
        html5: { hls: { withCredentials: false } },
        playbackRates: [0.7, 1.0, 1.5, 2.0], //倍速
        width: "804",
        height: "555",
        sources: [
          {
            type: "video/mp4", 
            src:'http://dev52.hbck.com.cn:97/home/roncoo/file/video/1330717271247609858.mp4'
             
          }
        ]
      }
    };
  },
  methods: {
    // listen event
    onPlayerPlay(player) {
      // console.log('player play!', player)
    },
    onPlayerPause(player) {
      // console.log('player pause!', player)
    },
    onPlayerEnded(player) {
      // console.log('player ended!', player)
    },
    onPlayerLoadeddata(player) {
      // console.log('player Loadeddata!', player)
    },
    onPlayerWaiting(player) {
      // console.log('player Waiting!', player)
    },
    onPlayerPlaying(player) {
      // console.log('player Playing!', player)
    },
    onPlayerTimeupdate(player) {
      // console.log('player Timeupdate!', player.currentTime())
    },
    onPlayerCanplay(player) {
      // console.log('player Canplay!', player)
    },
    onPlayerCanplaythrough(player) {
      // console.log('player Canplaythrough!', player)
    },
    // or listen state event
    playerStateChanged(playerCurrentState) {
      // console.log("player current update state", playerCurrentState);
    },
    // player is ready
    playerReadied(player) {
      // console.log("example 01: the player is readied", player);
    }
  }
};
</script>

<style scoped>
.video-player-box {
  width: 100%;
  margin: 0 auto;
  min-height: 4.18rem;
}
</style>