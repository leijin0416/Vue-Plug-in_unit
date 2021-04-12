# 1> Viewer轮子 -图片预览插件

`npm install v-viewer --S`

[vue-see 方法](https://github.com/zhaohaodang/vue-see) ---|--- [图片查看器 Viewer.js](https://blog.csdn.net/liang_wf/article/details/102561670)

## 2> main.js 全局引入

```js
import Vue from 'vue';
import Viewer from 'v-viewer';

import 'viewerjs/dist/viewer.css';
/**
 *  v-viewer
 *  引入图片查看器
 */
Vue.use(Viewer);
Viewer.setDefaults({
  defaultOptions: {
    zIndex: 9999
  }
});
```

## 3> 组件使用

```html
<div class="v-viewer-box">
  <viewer :images="photoImg" v-viewer="{toolbar: false}">
    <img class="v-img"
      :src="image"
      title="点击查看大图"
      alt="chats.png">
  </viewer>
</div>

<script>
  data() {
    return {
      photoImg: [],
      image1: '',
    }
  },
</script>
```
