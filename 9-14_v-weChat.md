# 即时通讯

Websocket 是应用层第七层上的一个应用层协议，它必须**依赖** 【HTTP 协议进行一次握手】 ，握手成功后，数据就直接从 TCP 通道传输，与 HTTP 无关了。即：websocket 分为【**握手和数据传输**】阶段，即进行了HTTP握手 + 双工的TCP连接。

[WebSocket滚动条自动置底](https://zhuanlan.zhihu.com/p/89906315) --|-- [WebSocket消息通知](https://blog.csdn.net/qq_37128049/article/details/96977671)
