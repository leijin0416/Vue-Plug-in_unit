# Websocket 即时通讯

Websocket 是应用层第七层上的一个应用层协议，它必须**依赖【HTTP 协议进行一次握手】** ，握手成功后，数据就直接从 TCP 通道传输，此后与 HTTP 无关了。

即：Websocket **分为【握手】和【数据传输】阶段**，即进行了HTTP握手 + 双工的TCP连接。连接一旦建立，客户端和服务端之间实时可以进行双向数据传输。

Websocket 其实是一个新协议，跟HTTP协议基本没有关系，只是为了兼容现有浏览器的握手规范而已，也就是说它是HTTP协议上的一种补充。

---

标签|内容
:-|:-:
WebSocket | [WebSocket |知乎解释 看!!!！](https://www.zhihu.com/question/20215561) - [WebSocket 滚动条自动置底](https://zhuanlan.zhihu.com/p/89906315) - [WebSocket 消息通知](https://blog.csdn.net/qq_37128049/article/details/96977671) - [WebSocket介绍 |掘金](https://juejin.im/post/6876301731966713869)
 | ——
[面试简介|特点 -Websocket](https://www.cnblogs.com/fan-1994716/p/11875730.html) |

## 应用场景

[关于对其理解介绍](https://www.zhihu.com/question/20215561)

**理解：** Websocket 约定了一个通信的规范，通过一个握手的机制，客户端和服务器之间能建立一个类似 tcp 的连接，从而方便它们之间的通信。在websocket出现之前，web交互一般是基于http协议的短连接或者长连接。websocket是一种全新的协议，不属于http无状态协议，协议名为"ws"。

场景：

```html
客户端：啦啦啦，我要建立Websocket协议，需要的服务：chat，Websocket协议版本：17（HTTP Request）
服务端：ok，确认，已升级为Websocket协议（HTTP Protocols Switched）
客户端：麻烦你有信息的时候推送给我噢。。
服务端：ok，有的时候会告诉你的。
服务端：balabalabalabala
服务端：balabalabalabala
服务端：哈哈哈哈哈啊哈哈哈哈
服务端：笑死我了哈哈哈哈哈哈哈
```

这样，只需要经过一次HTTP请求，就可以做到源源不断的信息传送了。解决了同步有延迟，而且还非常消耗资源的这种情况。

[Git 模板地址](https://github.com/leijin0416/Vue-Plug-in_unit/blob/master/vueTemplate/1_weChat.vue)
