## Web开发中路由实现原理
什么是路由:
    根据不同的url地址，展示不同的页面或者视图，路由可以保存当前的请求的资源路径和参数(可选)

### 1. server 路由实现
服务器端路由管理，常见的开发模式是前端根据url的不同，使用ajax发起异步请求，获取不同的页面资源，前端获取资源后更新页面。

server路由处理，一般是基于前后端没有分离的项目，html和数据绑定发生在后端(后端渲染)，有利于SEO，因为每次发送请求都需要获取资源，对服务器造成资源浪费，前端页面可能因为网速造成延迟，页面局部视图更新，ajax请求不同保存当前的请求状态，不能使用浏览器前进后退快捷键操作。

server路由处理实现类似于下面实现:不同的url请求路径，返回不同的模板
    app.get('', function (req, res) {
        res.header('Access-Control-Allow-Origin', '*');
        res.sendFile( __dirname + "/" + "index.html" );
    })

    app.get('/home.html', function (req, res) {
        res.header('Access-Control-Allow-Origin', '*');
        res.sendFile( __dirname + "/" + "pages/home.html" );
    })

    app.get('*', function (req, res) {
        res.header('Access-Control-Allow-Origin', '*');
        res.sendFile( __dirname + "/" + "pages/404.html" );
    })

[Demo](https://github.com/bojue/LearningList/tree/master/JavaScript/route_server)

### 2. Hash 路由实现原理

### 3. History Api 路由实现原理     

    History:接口允许操作浏览器的曾经在标签页或者框架里访问的会话历史记录。
> 
> H5对History进行了扩展，增加了两个重要的新的方法:
> History.pushState()
> History.replaceState()

#### 4. 比较
1.hash 路由实现: 兼容性比较好，url比较丑陋
2.History 路由实现: 比较直观，需要服务器端配合

#### 参考:
1.[前端路由的前生今世及实现原理](https://segmentfault.com/a/1190000011967786)