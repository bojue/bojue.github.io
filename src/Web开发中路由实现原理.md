# Web开发中路由实现原理
[Web开发中路由实现原理](#web开发中路由实现原理)
       - [服务端路由](#服务端路由)
        - [Hash路由](#hash路由)
         - [History](#history)
            - [前端路由实现比较](#前端路由实现比较)       - [参考:](#参考)
什么是路由:
    根据不同的url地址，展示不同的页面或者更新页面局部视图

### 服务端路由 
[Demo](https://github.com/bojue/LearningList/tree/master/JavaScript/route_server)

服务器端路由管理，常见的开发模式是前端根据url的不同，使用ajax发起异步请求，获取不同的页面资源，前端获取资源后更新页面。

后端路由处理，一般是基于前后端没有分离的项目，html和数据绑定发生在后端(后端渲染)，有利于SEO，因为每次发送请求都需要获取资源，对服务器造成资源浪费，前端页面可能因为网速造成延迟，页面局部视图更新，ajax请求不同保存当前的请求状态，不能使用浏览器前进后退快捷键操作。

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

![预览](https://github.com/bojue/Blogs/blob/master/assets/route_server.png)

### Hash路由 
[Demo](https://github.com/bojue/LearningList/tree/master/JavaScript/route_hash)

在单页面(SPA)开发中，通过Hash可以实现前端路由，hash路由形如:http:localhost:8100/#/home,
在url后缀存在#(锚点)，用来做页面定位，即根据页面id将该元素所在的区域展示在可视区域，#后面内容的改变不会发送请求到服务器。

前端路由需要实现一下:
1. 根据不同的hash展示对应的页面
2. 监听hash值的改变
3. 保存当前url的请求状态或者参数(比如页面刷新和分享链接，别人可以获取同样的内容)
4. 可以实现浏览器的前进后退功能

原理:
页面hash值可以通过 window.location.hash 属性获取，当url的hash值发生变化，会触发window对象的hashchange事件，通过监听 hashchange 事件，操作 window.location.hash 属性可以实现

Route.js

    function Route(params) {
        if(!params){
            console.log("请检查初始化数据")
            return false;
        }
        this.registeredRoute = [];
        this.contentId = params.contentId;
        this.routes = params.routes;
        this.devStatus = params.devStatus || 'none'
        this.otherRouter = params.otherRouter;
        this.init();
    } 
    
    Route.prototype = {
        constructor: Route,
        init: function()  {
            window.addEventListener('hashchange', (function(event){ 
                var currentHash = location.hash.substring(1) || this.otherRouter;
                var route = this.routes && this.routes.find(item => item['path'] === currentHash);
                if(this.devStatus === 'log') {
                    console.log("hash has been changed to:", currentHash)
                }
                if(route) {
                    this.activeRoute();
                    this.render(route)
                }

            }).bind(this))
            var initEvent = new Event('hashchange');
            window.dispatchEvent(initEvent);
        },
        //更新视图
        render(route) {
            if(!$){
                console.log("请确保项目正确引入jQuery")
                return false;
            }
            var _routeContent =  $(`#${this.contentId}`);
            if(_routeContent) {
                var currentView = `<div>current page: ${route['path']}</div> 
                <div>Params:${JSON.stringify(route['params'])} </div>
                <div>View:${route['component']}</div>`;
                _routeContent.html(currentView)
            }else {
                console.log("请绑定需要更新的视图区域");  
            }
        },
        //当前激活路由样式
        activeRoute() {
            var _routeList = $(".route") || [];
            for(var i=0; i< _routeList.length;i++) {
                var _item = _routeList[i];
                var _classList = _item.classList;
                var _defActice = !location.hash && _aDome['context'].getAttribute('data-route-param')==='home');
                var _aDome = $(_item.getElementsByTagName("a") && $(_item.getElementsByTagName("a")[0]);
                var _activeBool = _aDome['context'].getAttribute('data-route-param') === location.hash.substring(1)
                || _defActice;
                if(_activeBool) {
                    _classList.add('active')
                } else {
                    _classList.remove('active');
                }
            }
        }

    }

index.html 片段

    <div id="route-content"></div>
    <script>
    window.onload = function(){
       //路由列表
       var routes = [
            {
                path:'home',
                params: {
                    id:1
                },
                component: '<dev>home page </dev>'
            },
            {
                path:'list',
                params: {
                    id:2
                },
                component: '<dev>list page </dev>'
            },
            {
                path:'about',
                params: {
                    id:3
                },
                component: '<dev>about page </dev>'
            },
            {
                path:'info',
                params: {
                    id:4
                },
                component: '<dev>info page </dev>'
            }
        ];
        var _routeContent =  $("#route-content");
        if(!_routeContent) {
            console.log("请检查是否存在#route-content视图区域")
        }
        var route = new Route({
            contentId: 'route-content',
            routes: routes, //路由集合
            otherRouter: 'home',//默认路由
            devStatus: 'log' //设置开发模式
        });
    }
    </script>

![预览](https://github.com/bojue/Blogs/blob/master/assets/route_hash.png)

### History 
[Demo](https://github.com/bojue/LearningList/tree/master/JavaScript/route_history)     

window.history (window是浏览器的全局对象，所以window.history和history相同)是浏览器提供的用来记录和操作浏览器页面历史栈的对象的接口，提供了常用的属性和方法：

    history.back();     //回退
    history.go(-1);     //等同于history.back();
    history.forward();  //前进
    history.go(1); //等同forward()
    window.history.length; //历史栈页面的数量

H5对History进行了扩展，增加了两个重要的新的方法:

    History.pushState()  //浏览器历史记录压栈，增加一条历史记录
    History.replaceState() //浏览器历史记录最后一条数据更新，替换当前历史记录

操作pushState和replaceState方法会触发popstate事件，页面刷新不同浏览器事件监听存在差异，Chrome和Safari会触发popstate事件，Firefox不会触发。
我们可以通过pushState()，replaceState()记录和更新当前url和参数;
pushState(),replaceState()包含三个参数:

    state:存储当前参数的JSON
    title:短标题，浏览器实现不统一有些fireFox会直接忽略，可以设置为null做占位，
    url:当前url，更新浏览器url的值


### 前端路由实现比较

1. hash 路由实现: 兼容性比较好，url比较丑陋，不能使用浏览器栈操作前进后退
2. History 路由实现: 比较直观，需要服务器端配合，用户体验好，响应快，不需要每次发送服务器请求，通过操作浏览器历史栈完成页面跳转，低版本浏览器不支持H5特性，建议使用Hash

### 参考:
1.[前端路由的前生今世及实现原理](https://segmentfault.com/a/1190000011967786)