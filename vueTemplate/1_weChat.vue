<template>
    <div class="chat-box">
        <header>
            <div class="weui-flex">
                <div class="weui-cell-hd"><van-icon name="arrow-left" size="24" color="#666" @click="onPrevGoClick" /></div>
                <div class="weui-cell-bd">
                    <span v-if="status == 1">卖方</span>
                    <span v-else>买方</span>
                    {{count}}
                </div>
            </div>
        </header>
        <div class="msg-box" id="scrolldIV">
            <div class="v-time-box" v-if="createTime">
                <p><span>系统提示：订单匹配成功</span></p>
                <p>{{createTime}} </p>
            </div>
            <div
                v-for="(i,index) in list"
                :key="index"
                class="msg"
                :style="i.sender == id?'flex-direction:row-reverse':''" >
                <!-- 图标 -->
                <div class="user-head" v-if="i.sendState == 1">
                    <div class="head"
                        :style="` background: hsl(${getUserHead(i.sender,'bck')}, 88%, 62%); clip-path:polygon(${getUserHead(i.sender,'polygon')}% 0,100% 100%,0% 100%); transform: rotate(${getUserHead(i.sender,'rotate')}deg)`" ></div>
                </div>
                <!-- 文字 -->
                <div class="user-msg" v-if="i.sendState == 1">
                    <span
                        :style="i.sender == id?' float: right;':''"
                        :class="i.sender == id?'right':'left'" >{{i.message}}</span>
                </div>
                <!-- 订单状态 -->
                <div class="v-notice-box" v-if="i.sendState == 2">
                    <p><span>系统提示：{{i.message}}</span></p>
                    <p>{{i.createTime}} </p>
                </div>
            </div>
        </div>
        <!-- 输入框 -->
        <div class="input-box">
            <van-cell-group :border="false">
                <van-field
                    v-model="contentText"
                    @focus="onFocusClick"
                    rows="1"
                    autosize
                    type="textarea" 
                    ref="sendMsg" />
            </van-cell-group>
            <div class="btn" :class="{['btn-active']:contentText}" @click="sendText()">发送</div>
        </div>
        <!-- Tag提示 -->
        <div class="v-tag-box">
            <van-tag
                v-if="showTag.success"
                closeable
                size="medium"
                type="success"
                @close="closeTag('success')" >
                为营造良好环境，请文明聊天
            </van-tag>
        </div>
    </div>
</template>
 
<script>
import { sessionData } from '@/filters/local';
import { webWechatSelectChatRecord, } from '@/api/common';

export default {
    data() {
        return {
            ws: null,
            showTag: {
                primary: true,
                success: true,
            },
            // 后台参数
            order_info: {
                clientType: '1',     // 类型
                message: '',         // 文字内容
                sender: Number,      // 发送人
                receiver: Number,    // 接收人
                orderId: Number,     // 订单号
                time: '20',
                messageType: '',
            },
            status: 1,
            id: '',
            count: 0,
            list: [],          // 聊天记录的数组
            sendData: {},
            scrollHeight: true,
            createTime: '',
            contentText: "",   // input输入的值
        };
    },
    watch: {
        /**
         *  监听 list 聊天记录数组的变化；
         *  作用：
         *  - 判断如果数组长度 newer, older 不相等，就说明有新的聊天记录进来了，
         *    则改变 scrollHeight 状态，触发 scrollBottm() 事件；
         */
		"list": {
			handler: function(newer, older) {
                let _that = this;
				if (newer.length !== older.length) {
                    _that.scrollHeight = true;
				}
			},
	　　　　deep: true
		}
    },
    created() {
        let _that = this;
        let item = sessionData('get', 'getOrderInfo');      // 订单信息
        let userInfo = sessionData('get', 'getUserInfo');   // 用户信息
        let userId = _that.$route.query.ids;                // 0| 根据id判断 1买 2卖
        if (item && userInfo) {
            if (userId == 1) {
                _that.order_info.sender = item.buyerId;
                _that.order_info.receiver = item.sellerId;
                _that.count = item.sellerNickName;
                _that.id = item.buyerId;

            } else {
                _that.order_info.sender = item.sellerId;
                _that.order_info.receiver = item.buyerId;
                _that.count = item.buyerNickName;
                _that.id = item.sellerId;

            }
            _that.status = userId;
            _that.order_info.orderId = item.orderId;
            _that.getOrderInfoClick();
        }
        // console.log(item);
        // console.log(userId);
    },
    mounted() {
        let _that = this;
        _that.initWebSocket();
    },
    updated(){
        let _that = this;
        // 聊天记录自动定位到底部，置底。因为 updated 会无限更新
        _that.scrollBottm();
    },
    methods: {
        /**
         *  返回上一级
         */
        onPrevGoClick() {
            let _that = this;
            _that.$router.go(-1);
        },
        // 滚动条到底部
        scrollBottm() {
            let _that = this;
            let data = _that.list;  // 聊天记录
            let msg = document.getElementById('scrolldIV');   // DIV地址，获取聊天高度

            if (_that.scrollHeight && data.length > 0) {
                _that.$nextTick(() => {
                    // 监听聊天记录有变化，更新一次滚动条位置，把scrollHeight为false
                    _that.scrollHeight = false;
                    msg.scrollTop = msg.scrollHeight;
                })
                console.log('____END____');
            }
            // console.log(scrollTop);
            // console.log(msg.scrollHeight);
        },
        /**
         *  关闭 Tag 提示
         */
        closeTag(type) {
            let _that = this;
            _that.showTag[type] = false;
        },
        // 根据userID生成一个随机头像
        getUserHead(id, type) {
            let ID = String(id);
            if (type == "bck") {
                return Number(ID.substring(ID.length - 3));
            }
            if (type == "polygon") {
                return Number(ID.substring(ID.length - 2));
            }
            if (type == "rotate") {
                return Number(ID.substring(ID.length - 3));
            }
        },
        /**
         *  0\ 查询聊天记录
         */
        getOrderInfoClick () {
            let _that = this;
            let lang = sessionData('get', 'setLanguages') || 'zh_CN';
            let receiverUid = _that.order_info.receiver;
            let orderId = _that.order_info.orderId;
            // console.log(lang);
            
            webWechatSelectChatRecord({
                'receiverUid': receiverUid,
                'orderId': orderId
            }).then( res => {
                if (res.data.code == 200) {
                    let data = res.data.data;
                    if (data.length > 0) {
                        _that.createTime = data[0].createTime;
                        _that.list = data;
                    }
                }
                // console.log(res);
                // console.log(_that.sortData(areacodeList, 'name'));
            })
        },
        initWebSocket() {
            let _this = this;
            _this.connection();
        },
        /**
         *  1\ 发送聊天信息
         */
        sendText() {
            let _this = this;
            // _this.$refs["sendMsg"].focus();
            if (!_this.contentText) {
                return;
            }
            // 获取聊天参数对象
            let params = {
                sender: _this.order_info.sender,
                receiver: _this.order_info.receiver,
                orderId: _this.order_info.orderId,
                time: _this.order_info.time,
                messageType: '1',
                message: _this.contentText,
                clientType: _this.order_info.clientType,
            };
            // 及时调用 WebSocket send() 发送信息的方法
            _this.ws.send(JSON.stringify(params));
            // 及时清空输入框文字
            _this.contentText = "";
            // 及时获取聊天记录
            _this.getOrderInfoClick();
            // 及时更新位置
            setTimeout(() => {
                _this.scrollBottm();
            }, 30);
            // console.log(params);
        },
        /**
         *  2\ 进入页面创建 websocket 连接
         */
        connection() {
            let _this = this;
            let CHAT_WS = 'ws://192.55.165.42:9995/ws';
            let url = CHAT_WS;
            // 判断页面有没有存在websocket连接
            if (window.WebSocket) {
                let ws = new WebSocket(url);
                _this.ws = ws;
                // 触发定时器
                _this.createTimer();
                ws.onopen = function(e) {
                    console.log("服务器连接成功");
                };
                ws.onclose = function(e) {
                    _this.clearTimerPing();
                    console.log("服务器连接关闭");
                };
                ws.onerror = function() {
                    console.log("服务器连接出错");
                };
                ws.onmessage = function(e) {
                    // 接收服务器返回的数据
                    let resData = JSON.parse(e);
                    // console.log(resData);
                };
            }
        },
        /**
         *  3\ 创建定时器
         */
        createTimer() {
            let _that = this;
            if (window.timerPing) clearInterval(window.timerPing);
            window.timerPing = setInterval(() => {
                _that.getOrderInfoClick();
            }, 5000)
        },
        // 清楚ping定时器
        clearTimerPing() {
            if (window.timerPing) {
                clearInterval(window.timerPing);
            }
        },
        /** 
         *  输入法下软键盘遮挡输入框
         */
        onFocusClick() {
            var target = this;
            // setTimeOut(function() {
            //     target.scrollIntoView(true);
            // }, 30);
        },
    },
    beforeDestroy() {
        let _that = this;
        // 离开页面清除连接
        _that.ws.close();
    },
    destroyed () {
        let _that = this;
        _that.clearTimerPing();
    }
};
</script>
 
<style lang="scss" scoped>
.chat-box {
    margin: 0 auto;
    background: #fafafa;
    position: absolute;
    height: 100%;
    width: 100%;
    max-width: 100%;
    .v-tag-box {
        z-index: 99;
        position: fixed;
        bottom: 110px;
        left: 10px;
        /deep/.van-tag {
            padding: 4px 20px;
        }
    }
    header {
        position: fixed;
        width: 100%;
        height: 80px;
        font-weight: bold;
        font-size: 28px;
        border-bottom: 2px solid #eee;
        color: #333;
        background: #fff;
        z-index: 999;
        .weui-flex {
            padding: 0 0 0 20px;
            height: 100%;
            .weui-cell-bd { padding-right: 50px; text-align: center;}
        }
    }
    .msg-box {
        z-index: 9;
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        box-sizing: border-box;
        // height: calc(100% - 180px);
        padding-top: 84px;
        padding-bottom: 100px;
        overflow-y: scroll;
        .v-time-box, .v-notice-box {
            display: block;
            width: 100%;
            box-sizing: border-box;
            padding: 30px;
            font-size: 24px;
            text-align: center;
            color: #999;
            p {
                line-height: 1;
                span {
                    display: inline-block;
                    min-width: 400px;
                    padding: 10px;
                    border-radius: 6px;
                    background-color: #efefef;
                }
                &:nth-child(2) {padding-top: 10px;}
            }
        }
        .msg {
            min-height: 2.5rem;
            margin: 20px;
            position: relative;
            display: flex;
            justify-content: flex-start !important;
            .user-head {
                width: 60px;
                height: 60px;
                border-radius: 50%;
                background: #f1f1f1;
                display: flex;
                justify-content: center;
                align-items: center;
                .head {
                    width: 20px;
                    height: 20px;
                }
            }
            .user-msg {
                width: 80%;
                word-break: break-all;
                position: relative;
                z-index: 5;
                span {
                    display: inline-block;
                    padding: 14px 20px;
                    margin: 10px;
                    border-radius: 10px;
                    margin-top: 0.2rem;
                    font-size: 28px;
                    box-shadow: 0 0 10px #f5f5f5;
                }
                .left {
                    background: white;
                    animation: toLeft 0.5s ease both 1;
                }
                .right {
                    background: #53a8ff;
                    color: white;
                    animation: toright 0.5s ease both 1;
                }
                @keyframes toLeft {
                    0% {
                        opacity: 0;
                        transform: translateX(-10px);
                    }
                    100% {
                        opacity: 1;
                        transform: translateX(0px);
                    }
                }
                @keyframes toright {
                    0% {
                        opacity: 0;
                        transform: translateX(10px);
                    }
                    100% {
                        opacity: 1;
                        transform: translateX(0px);
                    }
                }
            }
        }
    }
    .input-box {
        z-index: 999;
        position: absolute;
        bottom: 0;
        left: 0;
        right: 0;
        min-height: 84px;
        padding: 10px 20px;
        background: #fafafa;
        box-shadow: 0 0 6px #ccc;
        display: flex;
        justify-content: space-between;
        align-items: center;
        /deep/.van-cell-group {
            width: 100%;
            .van-cell {
                padding: 14px 10px;
                border-radius: 10px;
            }
            .van-field__control {max-height: 50px; overflow: scroll;}
        }
        .btn {
            min-height: 72px;
            min-width: 120px;
            font-size: 24px;
            line-height: 3;
            color: white;
            text-align: center;
            border-radius: 6px;
            margin-left: 10px;
            transition: 0.5s;
            background: #e0e0e0;
        }
        .btn-active {
            background: $color-body-c;
        }
    }
}
</style>