## 1> 安装 

```php
npm install html2canvas --save

npm install qrcanvas --save     // 二维码
```

## 2> 新建qrCanvasUrl组件，供调用

### 思路：

- 1、template 模板分为两个部分。一个是要展示的背景盒子的素材画面（负极 z-index: -1），一个是用户需要保存的canvas 海报盒子（主级 z-index: 999）；

- 2、利用 `document.documentElement.clientHeight` 来动态获取不同手机的分辨率；

### 问题：

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

## 代码

```vue
<template>
    <div class="v-share">
        <p class="v-tips-top">{{$t('homePage_071')}}</p>
        <!-- 背景盒子 -动态高度 -->
        <div ref="box" 
            class="v-share-box" 
            :style="{height: fullHeight + 'px', backgroundImage: 'url(' + img + ')'}">
            <div class="v-row">
                <div class="i-qrcode">
                    <!-- 二维码 -->
                    <div id="qrcode"></div>
                </div>
                <p class="v-text">{{$t('homePage_072')}} {{title}}</p>
            </div>
        </div>
        <!-- 海报盒子 -遮罩层 -->
        <img :src="imgUrl" 
            alt="share_bg" 
            style="width:100%;height:100%;" 
            class="v-share-top" >
    </div>
</template>

<script>
import html2canvas from 'html2canvas'
import { qrcanvas } from 'qrcanvas'

import { getStore } from '@/common/localUtil'

export default {
    data() {
        return{
            fullHeight: document.documentElement.clientHeight,  //处理手机高度
            img: require('@/assets/img/hk/icon-share_bg1.png'),
            localeCut: 'zh-CN',
            title: '12',
            imgUrl:'',
            Url: ''
        }
    },
    methods:{
        drawUrl(){
            let that = this;
            let shareContent = that.$refs.box;      //需要截图的包裹的（原生的）DOM 对象 -'box'
            let width = shareContent.offsetWidth;   //获取dom 宽度
            let height = shareContent.offsetHeight; //获取dom 高度
            let canvas = document.createElement("canvas"); //创建一个canvas节点
            //var scale = window.devicePixelRatio; //定义任意放大倍数 支持小数
            let scale = 1; //定义任意放大倍数 支持小数
                canvas.width = width * scale; //定义canvas 宽度 * 缩放
                canvas.height = height * scale; //定义canvas高度 *缩放
                canvas.style.width = width + "px";
                canvas.style.height = height + "px";
                canvas.getContext("2d").scale(scale,scale); //获取context,设置scale
            /**
             *  html2canvas 默认参数
             */
            let opts = {
                scale: scale , // 添加的scale 参数
                useCORS: true,
                canvas:canvas,
            }

            /**
             *  qrcanvas 画二维码
             */
            let canvas1 = qrcanvas({
                data: decodeURIComponent(this.Url),
                size: 100
            })
            document.getElementById('qrcode').appendChild(canvas1)
            html2canvas(shareContent, opts).then(function(canvas) {
                // 将canvas转为base64图片(eg. data:image/png;base64,ijskjlkj)
            　　that.imgUrl = canvas.toDataURL()
        　　})
        }  
    },
	//生命周期 - 创建完成（可以访问当前this实例）
    created: async function() {
        let that = this
        let localeCut = getStore('localeCut')
        // 语言
        if (localeCut) {
            that.localeCut = localeCut
        }
        // 用户id
        let userId = getStore('userNameId')
        if (userId) {
            that.title = userId
        }
        that.Url = 'http://timchains.com/register?user=' + userId
	},
    mounted () {
        let _that = this
        _that.drawUrl()
    }
};
</script>

<style lang="scss" scoped>
.v-share {
    position: relative;
    .v-tips-top {
        z-index: 101;
        position: absolute;
        left: 50%;
        bottom: 5px;
        color: #fff;
        transform: translateX(-50%);
    }
    /* 背景盒子 */
    .v-share-box {
        z-index: 1;
        position: relative;
        width: 100%;
        background-repeat: no-repeat;
        background-position: center;
        background-size: cover;
        .v-row {
            z-index: 10;
            position: absolute;
            left: 50%;
            bottom: 20px;
            transform: translateX(-50%);
            color: #fff;
            .i-qrcode {
                display: block;
                width: 110px;
                height: 110px;
                padding: 5px;
                box-sizing: border-box;
                text-align: center;
                background-color: #fff;
            }
            .v-text {
                padding: 15px 0;
                font-size: 16px;
                font-weight: bold;
            }
        }
    }
    // 海报盒子
    .v-share-top {
        z-index: 100;
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