下载地址： 百度网盘 181

# 1> 安装 

`npm install -S file-saver xlsx（这里其实安装了2个依赖）`

`npm install -D script-loader`

# 2> main.js 全局引入

```js
/**
 *  需要引入 excel 文件夹
 */

import Blob from './excel/Blob.js'
import Export2Excel from './excel/Export2Excel.js'

```

# 3> 组件使用

```html
<Button type="primary" @click="onExportExcel" icon="md-person-add" style="margin-left: 15px" class="" :loading="loading" > 导出用户</Button>

<script>
    data() {
        return {
            tableData: [
                { index: "0", nickName: "我的随笔", name: "小明" },
                { index: "1", nickName: "我的评论", name: "小红" },
                { index: "2", nickName: "我的参与", name: "小兰" },
                { index: "2", nickName: "最新评论", name: "小芳" },
                { index: "2", nickName: "我的标签", name: "小兵" }
            ],
        }
    },
    // 方法集合
    methods: {
        /**
         *  excel 导出
         */
        onExportExcel () {
            require.ensure([], () => {
                /**
                *  1、require() -引入 JS
                */
                const { export_json_to_excel } = require("@/excel/Export2Excel.js");
                /**
                *  2、tHeader -excel表格文件标题
                */
                const tHeader = ["序号", "姓名", "手机"];
                /**
                *  2.1、tHeader -设置Excel的表格第一行的标题数据参数属性
                */
                const filterVal = ["index", "nickName", "name"];
                /**
                *  3、list -拿到后台数据 tableData
                */
                const list = this.tableData; 
                const data = this.formatJson(filterVal, list);
                /**
                *  4、export_json_to_excel -导出
                */
                export_json_to_excel(tHeader, data, "列表excel");
            });
        },
        formatJson(filterVal, jsonData) {
            return jsonData.map(v => filterVal.map(j => v[j]));
        },
    }

</script>
```

# 4> 效果图

![excel.jpg](https://upload-images.jianshu.io/upload_images/15813774-7d154578b42271c4.png?imageMogr2/auto-orient/strip|imageView2/2/w/483/format/webp)

[参考文档](https://www.jianshu.com/p/73105a06951e)