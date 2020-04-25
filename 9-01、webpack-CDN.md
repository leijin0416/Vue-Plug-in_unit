## CDN

- CDN 又叫内容分发网络，通过把资源部署到世界各地，用户在访问时按照就近原则从离用户最近的服务器获取资源，从而加速资源的获取速度。

- CDN 其实是通过优化物理链路层传输过程中的网速有限、丢包等问题来提升网速的。

https://github.com/zhouyupeng/vue-cli3-H5/blob/master/vue.config.js

https://www.v2ex.com/t/475746

https://cdn.jsdelivr.net/npm/vue@2.6.11/dist/vue.min.js
https://cdn.jsdelivr.net/npm/view-design@4.2.0/dist/iview.min.js

### 1、vue.config.js 修改

```js
const isProduction = process.env.NODE_ENV === 'production';

module.exports = {
    configureWebpack: config => {
        if (isProduction) {
            return {
                externals: {
                    'vue': 'Vue',
                    'vue-router': 'VueRouter',
                    'view-design': 'viewDesign',
                    'axios': 'axios',
                    'animate': 'Animate'
                }
            }
        }
    },
}
```

### 2、main.js 取消相关的引入

主要是删掉 ui 框架的引入和其他。vue 可以保留

```js
//import iView from 'iview';
import VueWechatTitle from 'vue-wechat-title'; // title
//import animated from 'animate.css'; // 动画
import VeLine from 'v-charts/lib/line.common'; // 图表可视化

//import 'iview/dist/styles/iview.css';
import "./style/reset.scss";
import "./style/common.scss";

//Vue.use(iView);
Vue.use(VueWechatTitle); //title
//Vue.use(animated);
```

### 3、public 文件夹下的 index.html 引入 CDN

引入 vue 的要放到最上层，Ui 靠后

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <!-- 启用360浏览器的极速模式(webkit) -->
    <meta name="renderer" content="webkit">
    <!-- 避免IE使用兼容模式 -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="MobileOptimized" content="width">
    <meta name="HandheldFriendly" content="true">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <link rel="stylesheet" type="text/css" href="http://unpkg.com/view-design/dist/styles/iview.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.2/animate.min.css">
    <title></title>
</head>
<body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
    <script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
    <script src="https://cdn.bootcss.com/vue-router/3.0.1/vue-router.min.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script type="text/javascript" src="http://unpkg.com/view-design/dist/iview.min.js"></script>
</body>
</html>
```
