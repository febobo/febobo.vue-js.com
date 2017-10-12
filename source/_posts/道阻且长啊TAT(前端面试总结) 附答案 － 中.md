---
title: 面试题解
date: 2017-10-12 17:14:42
tags:
---

京东二面
* JS面向对象之封装,继承,多态的体现和应用? 
* BST 
* promiseAll如何实现,以及如何实现多层异步回调? 
* 加油站问题(贪心算法) 
阿里二面
* XHR具体底层原理和API 
* 单例模式实现/设计模式 
* JSONP原理,回调过程 
* Latex怎么解析 
* hash算法实现,如何避免冲突,如何在冲突里检测 
* Generater 
* ES6怎么编译成ES5,css-loader原理,过程 
* ES6转成ES5的常见例子 
* 常见数据结构,常见算法 
* node多线程实现 
* 进程和线程 
阿里三面
* 对前端的理解 
* 说说rn,weex原理 
* 说说XHR 
* Jq的选择器引擎,$('.a .b')以及$('.a').find('.b') 
* MD5摘要算法其他用途 
百度一面
* 前端架构的理解 
* 前端框架的看法 
* 如何做前后端分离 
* 在不使用后端模板渲染的情况下,如何做前端数据直出(不使用异步请求数据)? 
* SSR适用什么场景?SPA首屏空白原因? 
* 前端构建过程原理,具体流程 
* vue2新增内容?独立构建（standalone）和运行时构建（runtime-only）的差别和应用? 
* 爬虫的实现原理?如何实现一个爬虫,如何解析文件? 
* 如何解决爬虫慢的问题? 
* 多线程如何保障各个线程的安全? 
* webpack工具和node了解哪些?node各个模块的底层原理? 
* CSS3实现卡片翻转? 
百度二面
* 说说职业规划? 
* 说说HTML难点,语义化的体现? 
* 说说CSS重要难点?如何实现垂直水平居中? 
* JS中this是什么?如何改变this? 
* 讲讲时间复杂度计算? 
* 算法题:假定26个字母对应1-26,将一串数字转成字母有多少种可能? 暴力解法复杂度是多少? 
(百度外卖业务:包含移动客户端(RN,hybrid),PC web端,H5,后台界面vue,桌面应用包含编辑器插件,前端构建工具,node中间层)
饿了么
* 盒模型之box-sizing 
* XHR原理及API 
* session和sessionStorage 
* map forEach(数组各个API细节) 
* dom事件代理,有什么优点? 
* 三种隐藏方式差别:visibility:hidden,display:none,opacity:0 
渲染上的差异: 1.将元素设置为display:none后，元素在页面上将彻底消失，元素本来占有的空间就会被其他元素占有，也就是说它会导致浏览器的回流和重绘。
2.设置元素的visibility为hidden，和display:none的区别在于，元素在页面消失后，其占据的空间依旧会保留着，所以它只会导致浏览器重绘而不会回流。  3.opacity:0,只是看不到元素,元素依然存在并且占有原有位置. 注: 事件绑定的差异: 1、display:none：元素彻底消失，不会触发绑定的事件. 2、visibility:hidden：无法触发其点击事件，有一种说法是display:none是元素看不见摸不着，而visibility:hidden是看不见摸得着，这种说法是不准确的，设置元素的visibility后无法触发点击事件，说明这种方法元素也是消失了，只是依然占据着页面空间。 3、opacity:0：可以触发点击事件，设置元素透明度为0后，元素只是相对于人眼不存在而已，对浏览器来说，它还是存在的，所以可以触发绑定事件 动画属性的差异: 1、display:none：完全不受transition属性的影响，元素立即消失 2、visibility：hidden：元素消失的时间跟transition属性设置的时间一样，但是没有动画效果. 3、opacity:0,动画属性生效,能够进行正常的动画效果. 代码演示地址
头条一面
1. inline和block元素列举,block和inline-block区别? 
2. span包含12px大小英文'abc',问span高度?baseline怎么理解? 
3. css实现自适应正方形? 
4. string查找第一个不重复字符.如果换成字符串呢? 
5. Angular数据绑定机制? 
6. 重绘和回流的理解 
7. querySelectAll和ByClassName所获取元素的区别?如何绑定事件? 
8. getComputedStyle用法? 
9. addEventListener绑定事件?参数不同的执行顺序. 
10. 正则匹配电话号码.exec,match,search用法?推荐一篇高质量正则文章 
11. 数据类型判断的方法,toString判断的来源? 
12. ES6 class原理? 
13. function rest参数长度?function.length如何判断与arguments对象长度区别? 
头条二面
* var a;typeof a;let a; 
* mvp,mvc,mvvm 
* vuex原理,watch对象 
* js defer async顺序,模块依赖(AMD,CMD差别) 
* 设计模式和应用 
* session和cookie 
* 清浮动,BFC 
* cookie存储在哪里?(memory) 
搜狐提前批
* BFC 
* Flexbox用法 
* 排序,找出最大三个数 
* 快排原理,复杂度计算 
* 继承的实现方式 
* vdom原理,实现 
* webpack打包是如何处理css图片的 
* 模块 
CVTE面试
* 四栏布局 
* 数组,数组对象去重 
* 数组查某个字符出现次数 
* 选择器权值 
* 右键菜单定制 
Ruff
* 函数不定参 
* 定时器原理 
* url=>渲染:script阻塞 
* 数据结构应用 
* 数据双向绑定(vue,ng原理) 
其他小公司:
1. 面向对象三要素 
2. 闭包 
3. 三栏布局 
4. 内联元素包含块元素的表现 
5. 三种隐藏方式的区别 
6. 求最大子数组和. 
7. 块元素和内联元素渲染区别? 
8. ul li实现计数. 
9. flex-box用法 
10. svg与canvas 
11. JPG,png图片特点,场景 
12. CSS写轮播 
13. 拖拽组建 
14. PV统计 
15. URL监听变化 
笔试
人人网
* 1.数组操作 
* 2.排序算法 
* 3.动态规划(书包问题) 
* 4.编程题(正则) 
CVTE
* 线程 
* 各类通信协议 
* 二叉树 
* 算法复杂度 
* 栈封装,利用栈对数组reverse(编程) 
美团
* 各类排序算法以及复杂度计算 
* Node的一些用法 
* 二叉树遍历 
* JS内部属性 
* 页面之间传参 
* 程序题:数组找公共元素 
网易
* 出入栈可能性计算 
* 数组去重 
