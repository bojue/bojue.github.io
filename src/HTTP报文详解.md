# HTTP报文详解
[HTTP报文详解](#http报文详解)          
- [报文组成](#报文组成)          
- [参考](#参考)

HTTP是超文本传输协议（HyperText Transfer Protocol），处在网络四层架构的应用层。
HTTP报文由简单字符串组成的文本，报文是对HTPP请求的额外描述，没有二进制格式的报文，报文分为请求报文和相应报文。

#### 报文组成

一个完整的报文包括三部分组成：

- 起始行

>  报文分为请求报文和相应报文，起始报文是报文的第一行字符串，起始报文说明报文要处理的内容，相应报文返回服务器发生了什么。
>
> 请求报文起始行由请求方法，资源路径，HTTP版本组成，中间用空格隔开。
>
> 相应报文起始行由HTTP版本，相应状态码，描述字段三部分组成，中间用空格可开。

- 首部字段

>  完整的报文包含0个或者多个首部字段，为了方便服务器解析，首部字段由:隔开的键和值两部分组成，首部以一个空行结束。首部字段可以为空。

- 报文主体

>  报文主体和首部用空行隔开，报文主体是服务器相应的资源，可以是任何形式的数据，包括字符串，html等文本形式，也可以是二进制形式，文本形式可以为空，比如HTTP协议的[OPTIONS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/OPTIONS)方法就是一个返回主体为空的请求。

![HTTP首部](https://github.com/bojue/Blogs/blob/master/assets/http_header.png)


#### 参考
1. [HTTP报文详情](https://www.cnblogs.com/klguang/p/4618526.html)
2. [HTTP权威指南](https://book.douban.com/subject/10746113/)