# vue-video-player

| 属性 | 描述 |
| ------ | ------ |
| autoplay | 如果true,浏览器准备好时开始回放。 |
| muted | 默认情况下将会消除任何音频。 |
| loop | 导致视频一结束就重新开始。 |
| preload | 建议浏览器在 video 加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持） |
| aspectRatio |  将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 （例如"16:9"或"4:3"） |
| fluid | 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。 |
| *sources* | 视频地址相关配置 |
| poster | 视频封面地址 |
| width | 播放器宽度 |
| notSupportedMessage |  允许覆盖Video.js无法播放媒体源时显示的默认信息。 |
| controlBar.fullscreenToggle | 全屏按钮 |

文档：https://github.com/surmon-china/vue-video-player

## 1、下载

```js
npm i vue-video-player --save
```

## 2、在main.js文件引入

```js
import VideoPlayer from 'vue-video-player'
require('video.js/dist/video-js.css')
require('vue-video-player/src/custom-theme.css')
Vue.use(VideoPlayer)
```

# 3、封装成组件 -videoPlayer：

```vue
<template>
    <video-player  
        class="video-player-box"
        ref="videoPlayer"
        :options="playerOptions"
        :playsinline="true"
        customEventName="customstatechangedeventname"

        @play="onPlayerPlay($event)"
        @pause="onPlayerPause($event)">
    </video-player>
</template>

<script>
  export default {
    data() {
        return {
            playerOptions: {
                autoplay: false, 
                muted: false, 
                loop: false, 
                preload: 'auto', 
                language: 'zh-CN',
                aspectRatio: '16:9', 
                fluid: true, 
                sources: [
                    {
                        type: 'video/mp4', 
                        src: require('@/assets/img/video.mp4')
                    }
                ],
                hls: true,
                poster: require('@/assets/img/hk/logo_lg.png'), 
                width: document.documentElement.clientWidth, 
                notSupportedMessage: '此视频暂无法播放，请稍后再试',
                controlBar: {
                    timeDivider: true,
                    durationDisplay: true,
                    remainingTimeDisplay: false,
                    fullscreenToggle: true 
                }
            }
        }
    },
    mounted() {
        //console.log('this is current player instance object', this.player)
    },
    computed: {
        player() {
            return this.$refs.videoPlayer.player
        }
    },
    methods: {
        onPlayerPlay(player) {
            // console.log('player play!', player)
        },
        onPlayerPause(player) {
            // console.log('player pause!', player)
        },
        playerStateChanged(playerCurrentState) {
            // console.log('player current update state', playerCurrentState)
        },
        playerReadied(player) {
            console.log('the player is readied', player)
        }
    }
  }
</script>

<style lang="scss" scoped>
/deep/.video-js.vjs-fluid {
    position: relative;
    .vjs-big-play-button {
        width: 2.2em;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
    }
}
</style>
```