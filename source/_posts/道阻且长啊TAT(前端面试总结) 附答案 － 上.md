---
title: 道阻且长啊TAT(前端面试总结) 附答案 － 上
date: 2017-08-18 17:14:42
tags:
---

## 腾讯一面
* 1.浏览器工作原理 

    * 用户界面－ 包括地址栏、后退/前进按钮、书签目浏览器引擎－ 用来查询及操作渲染引擎的接口
    * 渲染引擎－ 渲染界面:Firefox、Chrome和Safari是基于两种渲染引擎构建的，Firefox使用Geoko——Mozilla自主研发的渲染引擎，Safari和Chrome都使用webkit.
    * 网络－ 用来完成网络调用，例如http请求 UI 后端－ 用来绘制类似组合选择框及对话框等基本组件，具有不特定于某个平台的通用接口，底层使用操作系统的用户接口
    * JS解释器－ 解释执行JS代码
    * 数据存储－ 属于持久层，浏览器需要在硬盘中保存类似cookie的各种数据

* 2.Web安全,举例说明
    * xss
    * https
    * 混合内容
    * 同源策略 
* 3.状态码
    * 1xx 信息
    * 2xx 成功
    * 3xx 重定向
    * 4xx 客户端错误
    * 5xx 服务端错误
* 4.同源
    * 同端口
    * 同域名
    * 同协议
* 5.对象继承 
    * 所有继承都是基于原型(prorotype)的,由于js是动态语言,所以继承的方式也特别多
        * 原型链继承
        ```
            Child.prototype = new Parent();
        ```
        * 原型继承
        ```
            Child.prototype = Parent.prototype;
            Child.prototype.constructor = Child;
        ```
        * 拷贝继承(extend)

* 6.ES6历史以及新特性有哪些?
    * ......
* 7.promise原理
    * 理解promise 主要使用场景，理解promise 几个状态(pending,fulilled,rejected)
* 8.事件模型
    * DOM0级模型 `div.onclick = fn()`
    * DOM2级模型 `addEventListener(eventType, handler, useCapture)`
    * IE事件模型 `attachEvent( "eventType","handler")`

* 9.常见兼容性问题,列举(移动端/PC端)
* 10.性能优化
    * 减少http请求
    * 静态资源压缩
    * CDN加速
    * 减少全局变量
    * script标签放至body后

腾讯二面（机试）
类似百度搜索的提示框，兼容各大浏览器，可用键盘控制. 勉强憋了出来，但是挂掉了，犯了一些低级错误，显示经验不足． 面试官建议多看书，多写组件.
## 阿里一面
* 1.Ajax原理
    * create => open => send => onchange
* 2.浏览器解析过程
    * 流程： 解析html => 构建dom树->构建render树->布局render树->绘制render树
* 3.垂直居中
    * flex
    * line-height, text-center
    * postion , margin
    * display: table, vertical-align: middle
    * position, top:0, bottom: 0
* 4.数据类型判断
    * typeof
    * instanceof
    * toString
* 5.路由实现
    * location.hash+hashchange
    * history.pushState()+popState
* 6.数据本地存储
    * localStorage
    * indexDB
    * cookie
    * seesionStorage
* 7.跨域
    * jsonp
    * cors
* 8.数据双向绑定单向绑定优缺点
    * 双向绑定是自动管理状态的，对处理有用户交互的场景非常合适，代码量少，当项目越来越大的时候，调试也变得越来越复杂，难以跟踪问题
    * 单向绑定是无状态的, 程序调试相对容易, 可以避免程序复杂度上升时产生的各种问题, 当然写代码时就没有双向绑定那么爽了 
## 阿里二面
* 1.无线性能优化
    * 往上翻，有同样的问题
* 2.Tap事件,Touch
    * Tap 是zepto库封装好的事件，在移动端上可以替代click 事件，从而规避300ms问题
    * Touch 是移动端上的手势事件, 如 touchstart , touchend , touchmove
* 3.数据存储
    * 往上翻，有同样的问题
搜狐一面
* 1.Dom操作
    * 增删改查, 如：
        * document.elementById
        * document.querySelectAll
        * document.appendChild
        * docuemnt.removeChild
        * ......
* 2.移动布局方案
    * Rem, Em
    * flex
    * 百分比
    * media query
* 3.前后端协作
    * .....
* 4.原生Ajax实现过程
    * 往上翻，有同样的问题 
搜狐二面
* 1.单链表反转
    ```
    function ReverseList(pHead) {
        var pre = null;
        var next = null;
        while (pHead != null) {
        next = pHead.next;
        pHead.next = pre;
        pre = pHead;
        pHead = next;
        }
        return pre;
    }
    ```
* 2.快排
    ```
    const quickSort = (arr) => {
        if(arr.length < 1) return arr;
        let inx = Math.floor(arr.length/2);
        let pivot = arr.splice(inx,1)[0];
        let left = [];
        let right = [];
        for(let i=0; i<arr.length; i++){
        if(arr[i] < pivot){
            left.push(arr[i])
        }else{
            right.push(arr[i])
        }
        }

        return quickSort(left).concat(pivot,quickSort(right))
    }
    ```
* 3.即时通信(除了Ajax和websocket)
    * Comet技术：基于HTTP长连接的Web端实时通信技术
    * SSE：服务器发送事件,使用长链接进行通讯
* 4.服务器代理转发如何处理cookie(nginx) 
    ```
    proxy_cookie_domain localhost example.org;
    proxy_cookie_domain ~\.([a-z]+\.[a-z]+)$ $1;
    proxy_cookie_path /one/ /;
    proxy_cookie_path / /two/;
    ```
* 5.对象继承
    * __proto__
    * prototype
    * Object
* 6.this
    * 构造函数调用
    * apply,call使用
    * 对象的方法调用
    * 普通函数调用
* 7.rem布局的优缺点 


## 腾讯Alloy Team
一面(记录两个,其他都还好)
* 实现动画有哪些途径 
    * CSS3
    * JS帧动画,定时器,requestAnimateFrame
    * Canvas动画
    * SVG
    * 图片
* 对象继承的实现
    ```
        const Child = new Parent()
    ```
    ```
        Child.prototype = Parent.prototype
    ```
* web安全
    * https加密过程,证书用途
        * 服务端配置证书 -> 传送证书 -> 客户端解析证书 -> 传送加密信息 -> 服务端解密信息 -> 传输加密后信息 -> 客户端解密信息
    * xss几种形式,防范手段,过滤哪些字符?
        * 形式
            * 数据从一个不可靠的链接进入到一个web应用程序
            * 没有过滤掉恶意代码的动态内容被发送给web用户
        * 防范手段
            * 不信任任何用户的输入(过滤，转义)
            * 使用HTTP头指定类型
        * 过滤字符
            * <,>,",&quot
    * xsrf原理,实例,防范手段(Laravel的token)
    * Sql注入
        * 前端恶意提交sql代码,来篡改服务端的sql执行代码
* 性能优化
    * 代码优化(html,css,js)
    * 网络性能优化:
        1. Cache缓存之强制缓存和协商缓存
        2. CDN原理及应用
        3. HTTP压缩之gzip
* 上下文环境对象
* 设计模式(要求说出如何实现,应用,优缺点): 
    * 单例模式
        * 定义: 产生一个类的唯一实例
        * 实现: 
            ```
            const createMask = ()=>{
                let mask = null;
                return ()=> mask || document.appendChild(document.createElement('div'))
            }

            const mask = createMask();
            ```
        * 优点:
            1. 提供了对唯一实例的受控访问
            2. 避免对共享资源的多重占用
            3. 节约系统资源
        * 缺点:
            1. 扩展性差
            2. 职责过重
    * 工厂模式
        * 定义: 创建对象时无须指定创建对象的具体灯
        * 实现: 
            ```
            const Example = function(name,age){
                this.name = name || 'Tom',
                this.age = age || '18'
                this.say = function(){
                    console.log(`name:${this.name},age:${this.age}`)
                }
            }
            ```
        * 优点:
            1. 结构清淅,有效的封装变化 
            2. 对调用者屏蔽具体实现， 调用者只需关心产品的接口
            3. 降低耦合度
        * 缺点:
            1. 添加新的类，需要改写工厂类
    * 发布订阅模式
        * 定义: 定义对象间一种一对多的依赖关系，使得当每一个对象改变状态，则所有依赖于它的对象都会得到通知并自动更新。
        * 实现: 
            ```
            const Example = {};
            Example.clientList = [];
            Example.listen = function(fn){
                this.clientList.push(fn)
            }
            Example.trigger = function(){
                for(let i=0,fn; fn=this.clientList[i++]){
                    fn.apply(this,arguments)
                }
            }

            Example.listen(function(message){
                console.log(message) // 我发布了一个消息，此时订阅者会收到消息
            })  

            Example.trigger(function(){
                console.log('我发布了一个消息，此时订阅者会收到消息')
            }) 
            ```
        * 优点:
            1. 时间上的解藕 
            2. 对象之间的解藕
            3. 支持广播通信
        * 缺点:
            1. 如果一个观察目标对象有很多直接和间接的观察者的话，将所有的观察者都通知到会花费很多时间
            2. 如果在观察者和观察目标之间有循环依赖的话，观察目标会触发它们之间进行循环调用，可能导致系统崩溃
            3. 观察者模式没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而仅仅只是知道观察目标发生了变化
* 跨域(产生原因)
    * JSONP原理
        * 利用script标签没有跨域的漏洞进行第三方通信，第三方的响应数据为json，故称之为jsonp (json padding)
    * CORS如何设置
        * 通过改变response headers 设置白名单,来允许指定origin进行通信
    * Nginx代理
        ```
        location / {
            proxy_pass  xxx
        }
        ```
* 读过哪些框架源码?
* 如何写一个CSS库,要注意哪些东西?
    1. 总是类名优先
    2. 组件代码放在一起
    3. 使用一致的类命名空间
    4. 维护命名空间和文件名之间的严格映射
    5. 避免组件外的样式泄露
    6. 避免组件内的样式泄露
    7. 遵守组件边界
    8. 松散地整合外部样式