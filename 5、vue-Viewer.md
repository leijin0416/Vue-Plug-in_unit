# 1> viewer 安装 -图片预览插件

`npm install v-viewer --S`

[vue-see 方法](https://github.com/zhaohaodang/vue-see)

## 2> main.js 全局引入

```js
import Viewer from 'v-viewer';

/**
 *  v-viewer
 *  引入图片查看器
 */
Vue.use(Viewer);
Viewer.setDefaults({
  defaultOptions: {
    zIndex: 9999
  };
});
```

## 3> 组件使用

```html
<viewer :images="photoImg" v-viewer="{toolbar: false}">
  <div class="ivu-viewer">
    <img class="img"
      :src="image"
      title="点击查看大图"
      alt="chats.png">
  </div>
</viewer>

<script>
  data() {
    return {
      photoImg: [],
      image1: '',
    }
  },
</script>
```

## 4> 效果图

![viewer.jpg](http://120.79.222.255/icon-viewer.jpg)