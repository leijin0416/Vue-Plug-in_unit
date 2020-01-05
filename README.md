# Vue 个人插件资料整理

- 工作中用到的实用库。

# 1> 超实用 mavonEditor 富文本

[管方 Git 地址](https://github.com/hinesboy/mavonEditor)

![mavonEditor](http://120.79.222.255/icon-mavonEditor.png)

# 2> vue-i18n 语言切换

`npm install --s vue-i18n`

# 3> html2canvas 实现h5海报，qrCodejs2 生成二维码

`npm install --s html2canvas`

# 4> 3DES 请求接口加密

- 需要前后端协同配置Key

`npm install --s crypto-js`

# 5> v-viewer 点击预览图片

`npm install --s v-viewer`

# 6> vue-video-player 视频播放

`npm install --s vue-video-player`

```html
<script>
// main.js
import VideoPlayer from 'vue-video-player'
import 'vue-video-player/src/custom-theme.css'
import 'video.js/dist/video-js.css'

Vue.use(VideoPlayer)
</script>

<!-- 组件 -->
<template>
<div class="ivu-video">
    <video-player class="video-player vjs-custom-skin"
                ref="videoPlayer"
                :playsinline="true"
                :options="playerOptions">
    </video-player>
</div>
</template>

<script>
data() {
    return {
        playerOptions: {
            // 播放速度
            playbackRates: [0.5, 1.0, 1.5, 2.0], 
            // 如果true,浏览器准备好时开始回放。
            autoplay: false, 
            // 默认情况下将会消除任何音频。
            muted: false, 
            // 导致视频一结束就重新开始。
            loop: false, 
            // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
            preload: 'auto', 
            language: 'zh-CN',
            // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
            aspectRatio: '16:9',
                // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
            fluid: true,
            sources: [{
                //类型
                type: "video/mp4",
                //url地址
                src: require('@/assets/img/hk/video.mp4'),
            }],
            // 你的封面地址
            poster: '', 
            // 允许覆盖Video.js无法播放媒体源时显示的默认信息。
            notSupportedMessage: '此视频暂无法播放，请稍后再试',
            controlBar: {
                timeDivider: true,
                durationDisplay: true,
                remainingTimeDisplay: false,
                // 全屏按钮
                fullscreenToggle: true  
            }
        }
    }
},
</script>
<style>
.ivu-video {
    display: inline-block;
    width: 600px;
    height: 338px;
    text-align: center;
    line-height: 100px;
    border: 1px solid transparent;
    border-radius: 4px;
    overflow: hidden;
    background: #fff;
    position: relative;
    box-shadow: 0 1px 1px rgba(0, 0, 0, .2);
    margin-right: 4px;
}
</style>
```

