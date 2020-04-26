## CDN

- CDN 又叫内容分发网络，通过把资源部署到世界各地，用户在访问时按照就近原则从离用户最近的服务器获取资源，从而加速资源的获取速度。

- CDN 其实是通过优化物理链路层传输过程中的网速有限、丢包等问题来提升网速的。

[75CDN 官网-比较稳定](https://cdn.baomitu.com/) | [jsdelivr CDN官网-收录比较新](http://www.jsdelivr.com/) | [v2ex反馈](https://www.v2ex.com/t/475746)

[git 参考1](https://github.com/zhouyupeng/vue-cli3-H5/blob/master/vue.config.js) | 
[git 参考2](https://cdn.jsdelivr.net/npm/vue@2.6.11/dist/vue.min.js)

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
                    'axios': 'axios',
                    'view-design': 'viewDesign',
                    'animate': 'animate'
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
//import animated from 'animate.css'; // 动画
import VueWechatTitle from 'vue-wechat-title'; // title
import VeLine from 'v-charts/lib/line.common'; // 图表可视化

//import 'iview/dist/styles/iview.css';
import "./style/reset.scss";
import "./style/common.scss";

//Vue.use(iView);
//Vue.use(animated);
Vue.use(VueWechatTitle); //title
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

---

## 第2种：

externals 即为 webpack 所依赖的外部资源声明，键名为 webpack 给外部资源所定义的内部别名 alias，键值为外部资源所 export 暴露到全局的对象名称；

[文档解析](https://my.oschina.net/sallency/blog/2353892)

- 比如 cdn 引入的 vue 暴露的是 Vue object，vue-router 暴露的是 VueRouter 对象，vuex 暴露的是 Vuex 对象；

- 在 dev 代码时，import Vue from 'vue' 会优先加载本地模块，**但当我们打包 prod 代码时，webpack 便会提升 externals 的优先级，引用 CDN 资源，而忽略本地安装的模块。**

- devNeedCdn -控制本地环境和开发环境时的配载问题；

```js
const isDev = process.env.NODE_ENV;  //当前的环境
const isProduction = process.env.NODE_ENV !== 'development';

/**
 *  本地环境是否需要使用cdn
 *  － 打包时切换成 false
 */
const devNeedCdn = false;
// cdn链接
const cdn = {
    // cdn：模块名称和模块作用域命名（对应window里面挂载的变量名称）
    externals: {
        'vue': 'Vue',
        'vuex': 'Vuex',
        'vue-router': 'VueRouter',
        'axios': 'axios',
        'animate': 'animate',
        'video.js': 'VideoJs',
        'iview': 'Iview'
    },
    // cdn的css链接
    css: [
        'https://lib.baomitu.com/animate.css/3.7.0/animate.min.css'
    ],
    // cdn的js链接
    js: [
        'https://lib.baomitu.com/vue/2.5.7/vue.min.js',
        'https://lib.baomitu.com/vue-router/3.0.1/vue-router.min.js',
        'https://lib.baomitu.com/vuex/3.0.1/vuex.min.js',
        'https://lib.baomitu.com/axios/0.18.0/axios.min.js',
        'https://lib.baomitu.com/video.js/7.7.5/video.min.js',
        'https://lib.baomitu.com/iview/3.4.2/iview.js'
    ]
}
module.exports = {
    chainWebpack: (config) => {
        // ============注入cdn start============
        config.plugin('html').tap(args => {
            // 生产环境或本地需要cdn时，才注入cdn
            if (isProduction || devNeedCdn) args[0].cdn = cdn;
            return args
        });
        // ============注入cdn start============
    },
    configureWebpack: config => {
        if (isProduction || devNeedCdn) config.externals = cdn.externals;
        if (isDev === 'production') {
            config.plugins.push(
                // 压缩代码
                new CompressionPlugin({
                    algorithm: 'gzip',
                    filename: '[path].gz[query]',
                    test: productionGzipExtensions,
                    threshold: 10240,
                    minRatio: 0.8,
                    deleteOriginalAssets: false
                }),
                // 压缩提示
                new BundleAnalyzerPlugin()
            )
        }
    }
}

```

---

### （2）、main.js

- 需要屏蔽掉相关的 **“ import 引用 ”**；

- ui 可以单独保留引用样式 css，然后在 index.html 中可以只引用 js 文件了；

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import i18n from './locale'
import filters from './filters'
import VueWechatTitle from 'vue-wechat-title'  
import DrawerLayout from 'vue-drawer-layout'

// import Video from 'video.js'
// import 'video.js/dist/video-js.css'
// import animated from 'animate.css'
// import iView from 'iview'

import 'iview/dist/styles/iview.css'
import "./style/reset.scss"
import "./style/common.scss"

// 视频播放器
import VideoPlayer from 'vue-video-player';
require('video.js/dist/video-js.css');
require('vue-video-player/src/custom-theme.css');
Vue.use(VideoPlayer);

//Vue.use(iView);
//Vue.use(animated);
//Vue.prototype.$video = Video;

Vue.use(VueWechatTitle);
Vue.use(DrawerLayout);

Vue.config.productionTip = false;

// 全局过滤器
Object.keys(filters).forEach(filterName => {
    Vue.filter(filterName, filters[filterName])
})

/* eslint-disable no-new */
new Vue({
    router,
    store,
    i18n,
    render: h => h(App)
}).$mount('#app')

```

---

### （3）、index.html，引入 cdn 文件

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
        <!-- 使用CDN的CSS文件 -->
        <% for (var i in htmlWebpackPlugin.options.cdn &&
                  htmlWebpackPlugin.options.cdn.css) { %>
            <link href="<%= htmlWebpackPlugin.options.cdn.css[i] %>" rel="stylesheet" />
        <% } %>
        <!-- 使用CDN的CSS文件 -->
        <title>Green Planet</title>
	</head>
	<body>
		<div id="app"></div>
        <!-- built files will be auto injected -->
        <!-- 使用CDN的JS文件 -->
        <% for (var i in htmlWebpackPlugin.options.cdn &&
        htmlWebpackPlugin.options.cdn.js) { %>
            <script src="<%= htmlWebpackPlugin.options.cdn.js[i] %>"></script>
        <% } %>
        <!-- 使用CDN的JS文件 -->
	</body>
</html>
```
