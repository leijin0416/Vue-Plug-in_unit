# xlsx 安装

![excel.jpg](https://upload-images.jianshu.io/upload_images/15813774-7d154578b42271c4.png?imageMogr2/auto-orient/strip|imageView2/2/w/483/format/webp)

下载地址： 百度网盘 181 ---|--- [参考文档](https://www.jianshu.com/p/73105a06951e)

```html
<!-- 安装2个依赖 -->
npm install -S file-saver xlsx

npm install -D script-loader
```

## 构建导出文件 js

```js
/**
 *  新建 excel 文件夹，置入两个js
 */

'./excel/Blob.js'
'./excel/Export2Excel.js'
```

## 【1】 组件封装使用 exportExcels

- 利用 props ，父组件向子组件传递所需要导出相关数据参数

```vue
<template>
    <div class="v-excels-box">
        <Button type="primary" 
            @click="onExportExcel"  
            icon="ios-cloud-download-outline"
            style="margin-left: 15px" 
            class="btn ivu-btn-rd" 
            :loading="loading" >立即导出</Button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            loading: false
        }
    },
    components: {},
    props: {
        excelsTableData: {
            type: Array,
            default() {}
        },
        excelsTHeader: {
            type: Array,
            default() {}
        },
        excelsFilterVal: {
            type: Array,
            default() {}
        },
        excelsName: {
            type: String,
            default() {}
        }
    },
    // 监听属性 类似于data概念
    computed: {},
    // 监控data中的数据变化
    watch: {
        // 实时变化
        'UpDefaultList': {
            handler(newVal) {
                // this.uploadList=newVal;
            },
            deep: true
        },
    },
    // 生命周期 - 创建完成（可以访问当前this实例）
    created() {},
    // 生命周期 - 挂载完成（可以访问DOM元素）
    mounted() {},
    // 方法集合
    methods: {
        /**
         *  【 excel 导出 】
         *  @require - 利用 require() 引入 JS文件
         *  @excelsTHeader   - Excel 表格头
         *  @excelsFilterVal - Excel 表格头参数属性
         *  @excelsTableData - 后台完整数据
         *  @excelsName      - 设置导出文件名称
         *  @export_json_to_excel - 导出
         */
        onExportExcel () {
            let _that = this;
            _that.loading = true;
            if (_that.excelsTableData == []) return;
            require.ensure([], () => {
                let { export_json_to_excel } = require("@/excel/Export2Excel.js");

                let tHeader = _that.excelsTHeader;
                let filterVal = _that.excelsFilterVal;
                let list = _that.excelsTableData;
                let data = _that.formatJson(filterVal, list);
                let name = '绿色星球-' + _that.excelsName;

                export_json_to_excel(tHeader, data, name);
                _that.$Message.success({
                    content: '表格数据导出已完成',
                    duration: 3,
                    background: true,
                    onClose: () => {
                        _that.loading = false;
                    }
                });
            })
        },
        formatJson(filterVal, jsonData) {
            return jsonData.map(v => filterVal.map(j => v[j]));
        }
    }
}
</script>

<style lang="scss" scoped></style>
```

## 【2】 引用组件

```vue
<template>
    <div class="page ivu-form-box">
        <div class="ivu-header">
            <div class="ivu-paddtop-sm">
                <Button type="success" @click="onExprotModalClick" icon="ios-cloud-download-outline" style="" >导出用户</Button>
            </div>
        </div>
        <!-- 对话框 -导出用户 -->
        <Modal
            title="导出表格数据"
            v-model="exprotModal"
            @on-cancel="cancelExprotClcik"
            footer-hide
            :mask-closable="false">
            <div class="conter">
                <Form :model="params" label-position="left" :label-width="110" ref="">
                    <FormItem label="导出起始页：">
                        <Input v-model="params.pageNum" type="number" placeholder="开始页"></Input>
                        <p style="color: #999">默认填写从第1页开始；</p>
                    </FormItem>
                    <FormItem label="导出总条数：">
                        <Input v-model="params.pageSize" type="number" placeholder="填写数据导出的总条数"></Input>
                        <p style="color: #999">您可在分页处看到总条数，默认为12条；</p>
                    </FormItem>
                </Form>
            </div>
            <div class="demo-drawer-footer">
                <Button @click="onExprotDataClick" class="btn" :loading="loadingExprot" >确认</Button>
                <div class="v-exls" style="display: inline-block;" v-if="excelsTableData.length != 0">
                    <v-excels 
                        :excelsTableData="excelsTableData"
                        :excelsTHeader="excelsTHeader"
                        :excelsFilterVal="excelsFilterVal"
                        :excelsName="excelsName" />
                </div>
            </div>
        </Modal>
    </div>
</template>

<script>
import { adminUserAssetsList } from '@/api/common'

import exportExcels from "@/components/exportExcels/exportExcels"

export default {
    data() {
        return {
            loadingExprot: false,
            exprotModal: false,
            excelsTableData: [], // 表格数据
            excelsTHeader: [
                '用户编号', '用户昵称',  '手机号码',  '爱心值',  '绿化值',  '使用中养分', '未使用养分'
            ], // 表格行名
            excelsFilterVal: [
                'uid', 'userName',  'phone',  'loveAssets',  'green',  'use', 'unused'
            ],// 表格行名参数
            excelsName: '用户资产',
            params: {
                pageNum: 1,
                pageSize: 12,
                uid: '',
                phone: ''
            },
            sumPage: 1  //总页
        }
    },
    components: {
        'v-excels': exportExcels
    },
    // 监听属性 类似于data概念
    computed: {},
    // 监控data中的数据变化
    watch: {},
    // 生命周期 - 创建完成（可以访问当前this实例）
    created() {
        this.adminDataApi()
    },
    // 生命周期 - 挂载完成（可以访问DOM元素）
    mounted() {},
    // 方法集合
    methods: {
        /**
         *  导出表格 -Start-
         *  弹出对话框
         */
        onExprotModalClick () {
            let _that = this;
            _that.exprotModal = true;
        },
        onExprotDataClick () {
            let _that = this;
            let data = _that.params
            let { pageNum } = data
            let { pageSize } = data
            // console.log(data);
            if (pageNum !== '' && pageSize !== '') {
                _that.loadingExprot = true
                adminUserAssetsList({
                    'phone': '',
                    'uid': '',
                    'pageNum': pageNum,
                    'pageSize': pageSize
                }).then( res => {
                    if (res.data.code == 200) {
                        _that.excelsTableData = res.data.data.rows;
                    }
                    _that.loadingExprot = false
                    //console.log(res);
                })
            }
        },
        cancelExprotClcik () {
            let _that = this;
            _that.excelsTableData = [];
            _that.params.pageNum = 1;
            _that.params.pageSize = 12;
        },
        /** 导出表格 -End- */
    }
}
</script>

<style lang="scss" scoped></style>
```
