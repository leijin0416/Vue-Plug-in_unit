# Vue 个人插件资料整理

- 工作中用到的实用库。

# 1> 超实用 mavonEditor 富文本

![grow-number](https://github.com/hinesboy/mavonEditor/raw/master/img/cn/cn-common.png)

[Git 地址](https://github.com/hinesboy/mavonEditor)

# 2> html2canvas 实现h5海报

兼容问题：

1、高度：在ios和安卓机型上，不同分辨率下海报显示的高度会不一，需要调整

```html
<template>
    <div class="share">
        <!-- 素材-默认海报 -->
        <div ref="box" class="ivu-share">
            <img src="@/assets/img/hk/icon-share_bg1.jpg" alt="bg.png">
            <div class="ivu-row">
                <div id="qrcode" class="ivu-qrcode"></div>
                <p class="text">邀请用户名： {{title}}</p>
            </div>
        </div>
        <!-- html2canvas 海报 -->
        <div class="">
            <img :src="imgUrl" alt="share_bg.png" style="width:100%;height:100%;" class="ivu-share-bg">
        </div>
    </div>
</template>

<script>
import html2canvas from 'html2canvas'
import { qrcanvas } from 'qrcanvas'
import { getStore } from '@/common/localUtil'

export default {
    data() {
        return{
            posterHtmlBg: require('@/assets/img/hk/icon-share_bg1.jpg'),
            imgUrl:'',
            title: '',
            Url: ''
        }
    },
    methods:{
        draw(){
            let that = this;
            // shareContent 需要截图的包裹的（原生的）DOM 对象
            let shareContent = that.$refs.box;                  
            let width = shareContent.offsetWidth;               //获取dom 宽度
            let height = shareContent.offsetHeight;             //获取dom 高度
            let canvas = document.createElement("canvas");      //创建一个canvas节点
            let scale = 1;                                      //定义任意放大倍数 支持小数
                canvas.width = width * scale;                   //定义canvas 宽度 * 缩放
                canvas.height = height * scale;                 //定义canvas高度 *缩放
                canvas.style.width = width + "px";
                canvas.style.height = height + "px";
                canvas.getContext("2d").scale(scale,scale);     //获取context,设置scale
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
                size: 120
            })
            document.getElementById('qrcode').appendChild(canvas1)
            /**
             *  html2canvas 画海报
             *  需要调整 img 的高度
             */
            html2canvas(shareContent, opts).then(function(canvas) {
                // 将canvas转为base64图片(eg. data:image/png;base64,ijskjlkj)
            　　that.imgUrl = canvas.toDataURL()
        　　})
        }  
    },
    //生命周期 - 创建完成（可以访问当前this实例）
    created: async function() {
        let that = this
        let name = getStore('userName')
        that.title = name
        that.Url = 'http://timchains.com/register?user=' + name
    },
    mounted () {
        this.draw()
    }
}
</script>

<style lang="scss" scoped>
.share {
    position: relative;
}
/* 素材盒子 */
.ivu-share {
    z-index: 1;
    position: relative;
    width: 100%;
    height: 600px;
    background: #102131;
}
.ivu-row {
    position: absolute;
    left: 50%;
    top: 40%;
    transform: translate(-50%, -50%);
}
.ivu-row .ivu-qrcode {
    margin: 15px auto 5px;
}
.ivu-row .text {
    font-size: 12px;
}
/* canvas 图片盒子 */
.ivu-share-bg {
    z-index: 999;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 600px;
    background: #102131;
}
@media only screen and (max-width: 370px) {
}
</style>
```