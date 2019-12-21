# 1> 安装 

`npm install --s qrcodejs2`

# 2> 生成模板组件，供调用

问题：

1、在一个组件页面需要多次切换不同二维码时，会每次都生成一张二维码。

- 利用 showQRCodeDialog()方法，在数据发生变化时，先清空一遍原来已存在的数据

```html
<div class="ivu-code">
    <div id="qrCode" ref="qrCodeDiv" class="ivu-img"></div>
</div>

<script>
import QRCode from 'qrcodejs2'
import { mapState, mapMutations, mapActions, mapGetters } from "vuex"

export default {
    data() {
        return {
        }
    },
    props: { },
    components: { },
    //  监听属性 类似于data概念
    computed: {
		//取
		...mapGetters("localUser", ["developCode"])
    },
    //  监控data中的数据变化
    watch: {
		"developCode": {
			handler: function(newer, older) {
				if (newer == null) {
					return
				} else {
                    let _that = this;
                    _that.showQRCodeDialog(newer)
				}
			},
	　　　　deep: true
		}
    },
    //  方法集合
    methods: {
        /**
         *  清除上一次的二维码
         */
        showQRCodeDialog (type) {
            this.$refs.qrCodeDiv.innerHTML = ''
            this.QRcode(type)
        },
        /**
         *  二维码
         */
        QRcode(url) {
            let that = this
            let urls = url
            // console.log(url);
            if (urls) {
                new QRCode(this.$refs.qrCodeDiv, {
                    text: urls,
                    width: 150,
                    height: 150,
                    colorDark: "#333333",                   //二维码颜色
                    colorLight: "#ffffff",                  //二维码背景色
                    correctLevel: QRCode.CorrectLevel.L     //容错率，L/M/H
                })
            }
        },
    },
    //  生命周期 - 创建完成（可以访问当前this实例）
    created() {},
    //  生命周期 - 挂载完成（可以访问DOM元素）
    mounted() {
        this.QRcode();
    }
}

</script>

```