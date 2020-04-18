## 1> 下载安装npm

```js
npm install postcss-import postcss-loader postcss-px-to-viewport --s
```

## 2> 在 vue.config.js 中

- 响应式布局的常用解决方案：媒体查询（@media）、百分比、rem 和 vw/vh。

- rem 小缺陷是：在响应式布局中，必须通过js来动态控制根元素font-size的大小。但是兼容性好。

- css3中引入了一个新的单位 vw/vh，与视图窗口有关。换算 1px = （1/375）*100 vw。兼容性移动端 iOS 8 以上以及 Android 4.4 以上才获得支持。

| 方法 | 描述 |
| ------ | ------ |
| exclude | 默认false，可以（reg）利用正则表达式排除某些文件夹的方法,如 UI框架 |
| selectorBlackList | 要忽略并保留为px的类名(calss),对应声明中的像素单位不会转换,如 遇到不想px转换为vw的地方|
| viewportWidth | 视口宽度，这里设置为跟设计稿宽度一致 |
| viewportHeight | 视口高度，随便设置一个就可以 |
| unitPrecision | 转换后值的精度，3表示保留3位小数 |
| viewportUnit | 获取当前的播放时刻，返回的单位为秒。 |
| minPixelValue | 最小像素值，大于等于这个值才会转换 |
| mediaQuery | 是否转换媒体查询中的像素 |

```js
module.exports = {
    css: {
        loaderOptions: {
            postcss: {
                plugins: [
                    require('postcss-px-to-viewport')({
                        viewportWidth: 750,
                        viewportHeight: 1080,  
                        unitPrecision: 3,
                        viewportUnit: 'vw',
                        minPixelValue: 1,
                        mediaQuery: false,
                        exclude: /(node_module)/,
                        selectorBlackList: ['.ignore', '.hairlines']
                    })
                ]
            }
        }
    }
}
```

[postcss-px-to-viewport 文档](https://github.com/evrone/postcss-px-to-viewport/blob/master/README_CN.md) | [vw/vh 单位的兼容性](https://caniuse.com/) | [分析 VW布局](https://www.cnblogs.com/imwtr/p/9648233.html) | [分析2 VW布局](https://www.cnblogs.com/imwtr/p/9648233.html#top)

![viewport](https://segmentfault.com/img/bVbtNML?w=2506&h=860)

一般情况下，我们可以通过下述标签使得移动端在理想视口下布局：

```js
<meta id="viewport" name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1; user-scalable=no;">
```

物理像素线（也就是普通屏幕下 1px ，高清屏幕下 0.5px 的情况）采用 transform 属性 scale 实现：

```scss
.mod_grid {
    position: relative;
    &::after {
        // 实现1物理像素的下边框线
        content: '';
        position: absolute;
        z-index: 1;
        pointer-events: none;
        background-color: #ddd;
        height: 1px;
        left: 0;
        right: 0;
        top: 0;
        @media only screen and (-webkit-min-device-pixel-ratio: 2) {
            -webkit-transform: scaleY(0.5);
            -webkit-transform-origin: 50% 0%;
        }
    }
}
```