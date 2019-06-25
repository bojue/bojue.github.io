## WebSocket原理和应用

### 1. WebSocket 概念

WebScoket是HTML5提供，由同一个TCP连接实现的全双工通信协议，为了兼容基础的网络设施和网络策略，WebSocket共用了HTTP协议的握手过程，用户为了安全和保密对外仅开发有限网络端口，WebSocket也默认使用HTTP的默认端口(Http:80，Https:443)，WebSocket未来可能会定制自己的默认端口，用来实现更简单的握手过程(RFC 6455)，WebSocket的通信消息可以是文本和二进制格式

### 2. WebSocket 解决了什么问题

HTTP是单向应用层传输协议，采用请求相应的形式进行通信，并且请求只能由客户端发起服务器相应的形式交互，服务器不能主动发送消息到客户端。

HTTP通信协议开发中，客户端为了实时获取服务器最新的数据，一般采取两种方式：
1. 基于XHR的客户端请求轮询
2. 基于SSE的HTTP持久化连接

无论采用什么样的方式都有各自的缺点：

a. 轮询方案：每次轮询服务器数据不一定发生变化，网络通信也会经过三次握手和数据相应的过程；服务器数据改变，客户端可能没有及时的发起轮询请求，数据实时性不能保证。

b. 在长连接的方案中：当服务器没有数据更新的情况下，http连接一段时间内处于打开状态，损害服务器的整体性能，影响服务器的并发数目；SSE仅支持UTF-8内容，不支持二进制

### 3. WebSocket 原理

### 4. WebSocket 使用

### 5. 总结

有些基础的网络设备可能不支持WebSocket，在使用WebSocket可能存在兼容性问题，导致盲目连接升级，内容修改，意外缓存WebSocket数据帧，可以使用安全通道解决，即在WebSocket握手之前进行TLS连接，在服务器和客户端建立安全的网络通道。


#### 参考:
1. [WebSockets 简介](https://www.html5rocks.com/zh/tutorials/websockets/basics/#toc-introduction-lowlatency)
2. [MDN: WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)
3. [WebScoket 详细教程](https://www.cnblogs.com/jingmoxukong/p/7755643.html)
4. 《Web性能权威指南》