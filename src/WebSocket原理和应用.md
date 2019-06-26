# WebSocket原理和应用
[WebSocket原理和应用](#websocket原理和应用)
- [概念](#概念)  
- [WebSocket 解决了什么问题](#websocket-解决了什么问题)   
- [原理](#websocket-原理)       
- [使用](#使用)    
- [总结](#总结)  
- [参考](#参考)

### 概念

WebScoket是HTML5开始提供(HTML4不支持)，由同一个TCP连接实现的全双工通信协议。2011年被IETF定为标准RFC 6455。

### WebSocket 解决了什么问题

HTTP是单向应用层传输协议，采用请求相应的形式进行通信，并且请求只能由客户端发起服务器响应的形式交互，服务器不能主动发送消息到客户端，简单的HTTP请求无法实现服务器消息推送的功能。

HTTP通信协议开发中，客户端为了实时获取服务器最新的数据，一般可以采用以下多种方式实现：

+ 定时轮询：通过客户端定时发起请求，握手，服务器返回数据，关闭连接，定时重复
+ 长轮询：通过客户端发送请求，握手，连接打开直到服务器数据更新，返回数据，关闭连接，重复
+ 长连接：通过客户端发送请求，握手，连接打开知道服务器数据更新，返回数据，关闭连接

无论采取什么方案，在获取服务器实时数据的应用场景中存在一些可以改进的缺陷：

+ 定时轮询：每次轮询服务器数据不一定发生变化，网络通信也会经过三次握手和数据相应的过程；服务器数据改变，客户端可能没有及时的发起轮询请求，数据实时性不能保证。消息获取效率不高，浪费宽带和服务器资源

+ 长连接和长轮询：当服务器没有数据更新的情况下，http连接一段时间内处于打开状态，损害服务器的整体性能，影响服务器的并发数目；长轮询需要每次连接握手过程的时间和性能开销

为了优化网络性能开销，从底层解决服务器和客户端实时通信问题，WebSocket就是构建在TCP上不同于HTTP/HTTP2.0的新的协议方案，被W3C定位标准。

### 原理

WebSocket 是独立构建在TCP上的可以实现客户端和服务器持久化连接的网络协议，为了兼容基础的网络设施和网络策略，WebSocket共用了HTTP协议的握手过程，用户为了安全和保密对外仅开发有限网络端口，WebSocket也默认使用HTTP的默认端口,即Http 80，Https 443端口，WebSocket未来可能会定制自己的端口，用来实现更简单的握手过程(RFC 6455)。

![WebScoket和Ajax轮询](https://github.com/bojue/Blogs/blob/master/assets/websocket.png)
相对于以上双向通信方案WebSocket有以下优点：

+ 性能开销更少：握手完成后，客户端和服务器进行数据通信时，控制协议通信的数据头部信息更小
+ 实时性更好：WebSocket是全双工通信，连接保持连接状态，双方都可以主动向对方发送消息，数据通信存在更少时延。
+ 二进制支持：WebSocket支持文本和二进制数据内容，相对于HTTP可以很简单的实现对二进制数据的操作。
+ 支持扩展：WebSocket定义了扩展接口，WebSocket.extensions 属性查看服务器支持的扩展列表。
+ 支持压缩：可以通过压缩优化数据内容的大小，比如压缩utf-8内容Gzip压缩。

### 使用
WebScoket构造函数：

    WebSocket(url[, protocols]) //构造函数

构造函数存在四个常量，可以通过WebSocket.readyState对应连接状态。

| 常量 | 值 | 描述|
|-----|-----|-----|
| WebSocket.CONNECTING | 0 | 正在连接 |
| WebSocket.OPEN | 1 | 连接打开 |
| WebSocket.CLOSING | 2 | 连接正在关闭 |
| WebSocket.CLOSED | 3 | 连接关闭或者连接失败 |

构造函数接收两个参数：第一个是WebSocket的绝对地址，ws和wss都是WebSocket的schemaws表示普通文本通信，wss标示使用加密通道通信(TCP+TLS构建加密通道)；第二个可选参数为字符串数组，每个字符串对象表示一个客户端支持的子协议名称，子协议必须来自[IANA 注册表](https://www.iana.org/assignments/websocket/websocket.xml)，目前已经支持soap，rfb，wamp，MBWS.huawei.com等二十多种不同的子协议。

子协议协商：WebSocket对通信消息格式不做任何预处理，标记位仅标示消息内容是二进制还是文本没有提供更多的内容信息，没有类似HTTP请求通过首部信息沟通消息详情的机制，WebSocekt提供的子协议API用来沟通消息的信息，每次连接客户端通过传递子协议列表完成和服务器的子协议协商，告诉服务器客户端支持的子协议列表，服务器从子协议列表选择其中一个，可以通过WebSocket.protocol属性查看服务端支持的子协议名称

WebSocket封装了底层的消息处理和连接管理，对外提供简单的API实现网络的持久化连接，常用的API举例:

    WebSocket.binaryType //使用二进制类型连接
    WebSocket.bufferedAmount //浏览器未发送的队列数量
    WebSocket.url //WebSocket绝度路径
    WebSocket.protocol //服务器支持的子协议名称

通过这些API可以实现对WebSocket的属性设置和获取，比如  WebSocket.readState 获取连接状态:

+ 0：未建立连接
+ 1：已建立连接，可以通信
+ 2：连接正在关闭
+ 3：连接关闭/连接不能正常打开

结合API提供的方法可以实现对WebSocket的操作和优化，下面是一个简单的WebScoket方法的使用：

    // 创建实例
    const socket = new WebSocket('ws://localhost:8080'，['soap'] ;

    // 指定连接成功后的回调函数
    socket.addEventListener('open', function (event) {
        socket.send('Hello Server!');
    });

    // 指定连接接收消息回调函数
    socket.addEventListener('message', function (event) {
        console.log('Message from server ', event.data);
    });

    // 指定连接失败回调函数
    socket.addEventListener('error', function (event) {
        console.log('连接失败');
    });

    // 指定连接关闭回调函数
    socket.addEventListener('close', function (event) {
        console.log('关闭连接');
    });

### 总结

WebScoket解决了服务器到客户端主动发起消息的问题，相对于单向通信的网络HTTP协议，对于处理低延迟场景类似多人在线游戏，即使通信社交应用，消息推送，直播都是一个很好的技术方案。

WebSocket使用基于消息的API通信，发送文本或者二级制消息，WebScokat在通信过程中使用了自己定义的二进制分帧格式们，将一个消息可以切分成一个或者多个帧依次发送，服务器接收到完整的消息时接收到通知，不同的消息在多帧传递过程中不能交错传输，因此WebScoket会存在队首阻塞，即一个消息在传输过程中阻碍了其他消息的传输，可以通过控制消息大小，将大净何消息拆分成多个消息，优化消息发送顺序，TCP多路复用扩展等方式进行优化。

有些基础的网络设备可能不支持WebSocket，在使用WebSocket可能存在兼容性问题，导致盲目连接升级，内容修改，意外缓存WebSocket数据帧，代理网络可能屏蔽WebSocket,在WebSocket握手之前进行TLS连接，在服务器和客户端建立安全的网络通道，绕开中间代理实现可靠的数据传输。

常见的WebScoket第三方库[socket.io](https://socket.io/)，底层使用WebSocket和XMLHttprequest进行了封装，以兼容各种复杂情况。

[WebSocket浏览器兼容性](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%85%BC%E5%AE%B9%E6%80%A7)

## 参考:
1. [WebSockets 简介](https://www.html5rocks.com/zh/tutorials/websockets/basics/#toc-introduction-lowlatency)
2. [MDN: WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)
3. [WebScoket 详细教程](https://www.cnblogs.com/jingmoxukong/p/7755643.html)
4. [Web性能权威指南](https://book.douban.com/subject/25856314/)