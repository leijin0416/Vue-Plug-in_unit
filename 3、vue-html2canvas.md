# 分享海报和内容复制按钮

**`html2canvas` 图片 + `clipboard` 复制**

标签|内容
:-|:-:
 | [【掘金】 方法介绍](https://juejin.cn/post/6908255717317148685)
[html2canvas 方法概述](https://segmentfault.com/a/1190000007356836) | [【跨域】html2canvas 图片跨域问题](https://blog.csdn.net/feizhong_web/article/details/77067009?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.channel_param)

## 1> 下载安装npm

```js
npm install html2canvas --save

npm install qrcanvas --save     // 二维码
```

## 2> 新建qrCanvasUrl组件，供调用

### 【思路】

- 1、template 模板分为两个部分。一个是要展示的背景盒子的素材画面（负极 z-index: -1），一个是用户需要保存的canvas 海报盒子（主级 z-index: 999）；

- 2、利用 `document.documentElement.clientHeight` 来动态获取不同手机的分辨率；

### 【问题】

- 1、不同手机高度：在ios和安卓机型上，不同分辨率下海报显示的高度会不一，需要样式调整（上思路2可解决）;

- 2、由于 canvas 的原因，移动端生成出来的图片比较模糊的问题；

```js
// 根据需要设置 scaleSize 大小
const scaleSize = 2;
const newCanvas = document.createElement("canvas");
const target = document.querySelector('div');
const width = parseInt(window.getComputedStyle(target).width);
const height = parseInt(window.getComputedStyle(target).height);
newCanvas.width = width * scaleSize;
newCanvas.height = widthh * scaleSize;
newCanvas.style.width = width + "px";
newCanvas.style.height =width + "px";

const context = newCanvas.getContext("2d");
context.scale(scaleSize, scaleSize);
html2canvas(document.querySelector('.demo'), { canvas: newCanvas }).then(function(canvas) {
    // 简单的通过超链接设置下载功能
    document.querySelector(".btn").setAttribute('href', canvas.toDataURL());
}
```

## qrHtmlCanvas.vue

**`.replace(/^(http)[s]*(\:\/\/)/, 'https://images.weserv.nl/?url=')`** -处理图片 403 的问题

```vue
<template>
    <!-- 海报  -->
    <div class="v-share">
        <!-- 背景盒子 -->
        <div ref="box" class="v-share-box">
            <div class="v-flex" :style="{height: fullHeight}">
                <div class="v-flex-bd" v-if="payImgUrl">
                    <img :src="payImgUrl" alt="payImgUrl.png" class="v-img-box">
                </div>
            </div>
        </div>
        <!-- 海报盒子 -遮罩层 -->
        <img :src="imgUrl"
            ref="imgBg"
            alt="share_bg"
            class="v-share-top" v-if="imgUrl" >
    </div>
</template>

<script>
import './html2canvas.js';
import html2canvas from 'html2canvas';
import { qrcanvas } from 'qrcanvas';

import { sessionData } from '@/filters/local';
import { getStore } from '@/common/localUtil';

export default {
    data() {
        return{
            imgBg: require('@/assets/img/hk/i-share_bg_cn.png'),
            fullHeight: 'auto',         // 计算处理手机高度
            localeCut: 'zh_CN',
            imgUrl:'',                  // 背景海报
            qrCodeUrl: '',              // 生成二维码
            payImgUrl: '',              // 需要保存的背景海报
        }
    },
    props: {
        payImgUrls: {
            type: String,
            default: ""
        }
    },
    // 创建完成（访问当前this实例）
    created: async function() {
        let _that = this;
        _that.payImgUrl = '';
        let url = sessionData('get', 'setpayImgUrl');  // 图片地址
        if (url) {
            // 处理图片403
            let replaceUrl = url.replace(/^(http)[s]*(\:\/\/)/, 'https://images.weserv.nl/?url=');
            _that.payImgUrl = replaceUrl;
            // 挂载
            _that.drawUrl();
        }
        // console.log(url);
    },
    mounted () {
    },
    methods:{  
        async drawUrl(){
            await setTimeout(() => {
                let _that = this;
                let shareContent = _that.$refs.box;      // 需要截图的包裹的（原生的）DOM 对象 -'box'
                let width = shareContent.offsetWidth;   // 获取dom 宽度
                let height = shareContent.offsetHeight; // 获取dom 高度
                let canvas = document.createElement("canvas"); // 创建一个canvas节点
                //var scale = window.devicePixelRatio; // 定义任意放大倍数 支持小数
                let scale = 2; // 定义任意放大倍数 支持小数
                    canvas.width = width * scale; // 定义canvas 宽度 * 缩放
                    canvas.height = height * scale; // 定义canvas高度 *缩放
                    canvas.style.width = width/2 + "px";
                    canvas.style.height = height/2 + "px";
                    canvas.getContext("2d").scale(scale,scale); // 获取context,设置scale
                // allowTaint: true
                let opts = {
                    scale: scale/2, // 添加的scale 参数
                    useCORS: true,//允许跨域图片
                    taintTest: true,//是否在渲染前测试图片
                    canvas: canvas,
                    dpi: 1000,
                    proxy: ''
                }
                html2canvas(shareContent, opts).then(function(canvas) {
                    //const dataURL = canvas.toDataURL("image/png");
                    let dataURL = canvas.toDataURL();
                　　 _that.imgUrl = dataURL;
            　　});

            }, 1000);
        },
    },
};
</script>

<style lang="scss" scoped>
.v-share {
    position: relative;
    // 背景盒子
    .v-share-box {
        z-index: 1;
        position: relative;
        .v-flex {
            display: flex;
            flex-direction: column;
            .v-flex-bd {
                flex: 1;
                overflow: hidden;
                .v-img-box {
                    vertical-align: top;
                    line-height: 0;
                }
            }
            .v-flex-ft {
                position: relative;
                display: block;
                min-height: 210px;
                padding: 30px 40px;
                text-align: right;
                color: #333;
                background-color: #fff;
                .v-qrcode-box {
                    display: none;
                }
                .v-text-box {
                    @include tb;
                    left: 40px;
                    font-size: 20px;
                    text-align: left;
                    .v-text {
                        color: #666;
                    }
                }
            }
        }
    }
    // 海报盒子
    .v-share-top {
        z-index: 99;
        position: absolute;
        left: 0;
        top: 0;
        right: 0;
        bottom: 0;
    }
}

@media only screen and (max-width: 370px) {
}
</style>
```

---

## 3> 处理按钮复制功能

1、下载安装npm

```js
npm install clipboard --save
```

2、实际操作

```vue
<template>
    <div class="v-page">
        <div class="weui-flex">
            <div class="weui-cell-bd">
                <p>{{$t('homePage_068')}}</p>
                <!-- 需要复制的内容 -->
                <p>{{qrCodeUrl}}</p>
            </div>
            <div class="weui-cell-ft">
                <!-- 按钮复制 -->
                <van-button
                    :data-clipboard-text="qrCodeUrl"
                    @click="onClipBoardClcik"
                    type="default"
                    size="small"
                    class="v-backgcolor ctrlBtn" >复制</van-button>
            </div>
        </div>
    </div>
</template>

<script>
import Clipboard from "clipboard";

export default {
    data() {
        return {
            qrCodeUrl: 'https://youzan.github.io/vant/#/zh-CN/list',
        }
    },
    components: {
    },
    computed: {
        // 取
        ...mapGetters("localUser", ["developCode"])
    },
    watch: {
    },
    // 生命周期 - 创建完成
    created () {
        let _that = this;
        _that.onQrCodeUrl();
    },
    // 页面初始化
    mounted(){
    },
    methods: {
        /**
         * 复制链接
         * - .ctrlBtn：            复制内容的位置class
         * - data-clipboard-text： 复制的内容
         */
        onClipBoardClcik(){
            let that = this;
            let clipboard = new Clipboard('.ctrlBtn');  // 按钮
            clipboard.on('success', function(e) {
                that.$toast({
                    duration: 2000,
                    message: '复制成功'
                });
                e.clearSelection();
                // 释放内存  
                clipboard.destroy()  
            });
            // 失败回调
            clipboard.on('error', function(e) {
                console.log('error');
                // 释放内存  
                clipboard.destroy()
            });
        },
    },
}
</script>

<style lang="scss" scoped></style>
```
