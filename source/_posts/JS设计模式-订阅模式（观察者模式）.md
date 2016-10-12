---
title: JS设计模式-订阅模式（观察者模式）
date: 2016-10-11T16:16:55.000Z
tags: null
description: >-
  不管是在现实也好，程序世界也罢，发布订阅模式从来都是于我们形影不离，下面我们举个栗子
  老王很想买一张CD,但老板却告知他CD已经卖完了，要等下次进货的时候才会有，于事老王就留了老板号码，每天打一个电话过去问老板CD有没有到货，当然想要CD不只有老王一个，
  还有老张，老李等......，他们也跟老王一样， 每天打一个电话过去问老板， 老板烦了，
  于是就想到以后有人来问CD就将那人的电话留下，然后告知在CD到货的时候给他们打电话
---

# Subscribe 订阅模式 （观察者模式）

## 现实中的发布－订阅模式

不管是在现实也好，程序世界也罢，发布订阅模式从来都是于我们形影不离，下面我们举个栗子 老王很想买一张CD,但老板却告知他CD已经卖完了，要等下次进货的时候才会有，于事老王就留了老板号码，每天打一个电话过去问老板CD有没有到货，当然想要CD不只有老王一个， 还有老张，老李等......，他们也跟老王一样， 每天打一个电话过去问老板， 老板烦了， 于是就想到以后有人来问CD就将那人的电话留下，然后告知在CD到货的时候给他们打电话

## 发布－订阅模式的作用

上面的栗子我们可以看到想买CD的人就相当于程序里的订阅者，老板就相当于程序里的发布者 解决了什么 ？

- 老王他们不会每天打电话来问了，顿时心情大好了
- 发布者与订阅者没有藕合了，以后有想买CD的只要把电话留下，在适当的时间，发布者只要通知这些消息订阅者就好了

## DOM事件

实际上，我们经常用到的事件绑定就是发布订阅模式

```
document.body.addEventListener('click',function(){
  console.log('hello')
},false)
```

在这里我们想在用户点击的时候做出相应的处理，但是我们不知道用户在什么时候去点击，所以我们去订阅body上的click事件，在这里我们还可以去随意增加订阅者，这样并不影响我们的发布者

```
document.body.addEventListener('click',function(){
  console.log('hello2')
},false)
document.body.addEventListener('click',function(){
  console.log('hello3')
},false)
```

## 自定义事件

除了DOM事件，我们还经常会实现一些自定义事件 我们一步一步来实现一个自定义事件

- 谁是发布者
- 给发布者添加一个缓存列表
- 发布者遍历缓存列表，依次触发存放的订阅者回调

  ```
  var cdBoss = {} //cd店老板
  cdBoss.clientList = []; // 存放订阅者的回调
  cdBoss.listen = function(fn){ // 增加订阅者
  this.clientList.push(fn); // 将订阅者的cb存进缓存列表
  }
  cdBoss.trigger = function(){ // 发布消息
  for(var i=0,fn; fn= this.clientList[i++];){
    fn.apply(this,arguments)
  }
  }
  ```

  老王跟老李订阅一个想买cd的消息

  ```
  cdBoss.listen(function(cdName){
    console.log('老王订阅cd' + cdName) // 再见Jay
  })
  cdBoss.listen(function(cdName){
    console.log('老李订阅cd' + cdName) // 再见Jay
  })
  ```

  cd店老板给订阅者推送一个消息

  ```
  cdBoss.trigger('再见Jay')
  ```

  上面我们已经实现在最简易版的发布订阅，但其实是存在问题的，老王跟老李订阅的不同的cd ,但是cd店老板只要一发布，不管老王有没有相关的cd都会收到通知，这是我们不想看到的，那么我们有必要给订阅者增加一个标识（key）,这样订阅者就只会收到自己感兴趣的通知，接下来我们改写代码 ``` var cdBoss = {} //cd店老板 cdBoss.clientList = {}; // 存放订阅者的回调 cdBoss.listen = function(key,fn){ // 增加订阅者 if(!this.clientList[key]){ // 如果没有此类订阅，就给该类订阅增加一个缓存列表 this.clientList[key] = [] } this.clientList[key].push(fn); // 将订阅者的回调存进缓存列表 } cdBoss.trigger = function(){ // 发布消息 var key = Array.prototype.shift.call(arguments), // 取出消息类型

  ```
  fns = this.clientList[key] // 取出该类消息订阅集合
  ```

  if(!fns || !fns.length) return; // 如果该类消息没有订阅直接返回

  for(var i=0,fn; fn=fns[i++]){ fn.apply(this,arguments) } }

cdBoss.listen('Jay',function(cdName){ console.log('老王订阅cd' + cdName) }) cdBoss.listen('JJ',function(cdName){ console.log('老李订阅cd' + cdName) }) cdBoss.trigger('Jay','乱世情人'); cdBoss.trigger('JJ','天下大乱');

```
经过我们的改写，老王再也不用担心收到不感兴趣的推送了
## 发布－订阅的通用实现
几天过去了，老王还没有收到cd店老板消息，可老王迫不及待想要听新cd了,于是老王就想换一家店去问问，那么问题来了，另一家老板是不是又得重新跟老王解释一遍同样的问题，不过还好JS做为一门解释性语言，动态给对象添加职责再容易不过，我们再来改写一下代码
```

//先把发布，订阅的功能提取出来 var event = { clientList : {}, listen : function(key,fn){ if(!this.clientList[key]){ // 如果没有此类订阅，就给该类订阅增加一个缓存列表 this.clientList[key] = [] } this.clientList[key].push(fn); // 将订阅者的回调存进缓存列表 }, trigger : function(){ var key = Array.prototype.shift.call(arguments), // 取出消息类型 fns = this.clientList[key] // 取出该类消息订阅集合

```
if(!fns || !fns.length) return; // 如果该类消息没有订阅直接返回

for(var i=0,fn; fn=fns[i++];){
  fn.apply(this,arguments)
}
```

} }

// 再定义一个function ,目的就是让所有对象都可以通过个function 来订阅，发布 var installEvent = function(obj){ for(var i in event){ obj[i] = event[i] } }

// 再来试一试 var cdBoss = {}; // 某一cd店老板 installEvent(cdBoss); cdBoss.listen('Jay',function(cdName){ console.log('老王订阅cd' + cdName) }) cdBoss.listen('JJ',function(cdName){ console.log('老李订阅cd' + cdName) }) cdBoss.trigger('Jay','乱世情人'); cdBoss.trigger('JJ','天下大乱');

```
## 取消订阅的事件
由于老王之前再多家cd店订阅过消息，虽然老李已经从美女老板手里买到cd ，但是隔壁几位王姓老板还是给他发推送消息，显然老李是不开心的，那能不能把订阅过的消息取消掉了，答案是肯定的，我们来看一下下面的代码
```

// 接着给event 添加一个remove 方法 event.remove = function(key,fn){ var fns = this.clientList[key]; if(!fns) return; //如果该key 没有对应方法直接返回 if(!fn){ fns.length = 0 // 如果没有传入具体的回调，则清空该类下所有订阅消息 }else{ for(var l=fns.length-1;l>=0;l--){ var _fn = fns[l]; if(_fn == fn){ fns.splice(l,1) // 删除订阅者的回调函数 } } } }

cdBoss.listen('JJ',fn1=function(cdName){ console.log('老李订阅cd' + cdName) // 只有这个订阅会收到消息 }) cdBoss.listen('JJ',fn2=function(cdName){ console.log('老李订阅cd' + cdName) }) cdBoss.remove('JJ',fn2) cdBoss.trigger('JJ','天下大乱');

```
## 全局的发布－订阅对象
老王忽然想起来自己还想要买另一张cd，但是现在这种模式下，老王还要去一次cd店跟老板订阅一下，显然这是一种资源浪费，如果我们有一种中介平台，我们不再去关心谁订阅，谁发布，岂不快哉，中介者存在的前提就是发布者跟订阅者都得知道这个生物的存在，我们用代码来实现一下
```

var Event = (function(){ var clientList = {}, listen, trigger, remove; listen = function(key,fn){ if(!this.clientList[key]){ // 如果没有此类订阅，就给该类订阅增加一个缓存列表 this.clientList[key] = [] } this.clientList[key].push(fn); // 将订阅者的回调存进缓存列表 } trigger = function(){ var key = Array.prototype.shift.call(arguments), // 取出消息类型 fns = this.clientList[key] // 取出该类消息订阅集合

```
if(!fns || !fns.length) return; // 如果该类消息没有订阅直接返回

for(var i=0,fn; fn=fns[i++];){
  fn.apply(this,arguments)
}
```

} remove = function(){ var fns = this.clientList[key]; if(!fns) return; //如果该key 没有对应方法直接返回 if(!fn){ fns.length = 0 // 如果没有传入具体的回调，则清空该类下所有订阅消息 }else{ for(var l=fns.length-1;l>=0;l--){ var _fn = fns[l]; if(_fn == fn){ fns.splice(l,1) // 删除订阅者的回调函数 } } } }

return { listen : listen, trigger : trigger, remove : remove, clientList : clientList } })()

Event.listen('Jay',function(cdName){ console.log('老王订阅cd'+cdName) }); Event.trigger('Jay','乱世情人')

```

## 小试牛刀－模块间的通信
通过老王买cd，我们心里对发布订阅有了初步认识，下面我们用一个小例子通过发布订阅模式来实现模块间的通信，我们有父子两个模块，在子模块里点击一个按钮每次累加一个数，父模块随时更新到页面中
```

<!DOCTYPE html>





<div id="parent">0</div>

<div>
  <button id="children">click me</button>
</div>



<script>
    var parent = (function(){
        Event.listen(&#39;add&#39;,function(count){
            document.getElementById(&#39;parent&#39;).innerHTML = count
        })
    })()
    var children = (function(){
        var count = 0;
        document.getElementById(&#39;children&#39;).onclick = function(){
            Event.trigger(&#39;add&#39;,count++)
        }
    })()
</script>

 ```

## 思考1－必须先订阅再发布吗

从上面几个例子来看，都是先订阅后才能收到发布者的消息， 如果发布者先发布了一条消息，订阅者才去订阅， 那么这条消息就消失在了这片星晨大海，从程序的角度来讲，是会有先发布在订阅这种场景存在的，比如qq的离线消息，现在我们来分析一下这个是怎么做到的

- 首先我们要见新建一个存放离线事件的堆栈
- 当事件发布时，如果些时还没有订阅者来订阅这个消息，我们就暂时把这个发布事件的动作扔到一个函数里
- 当事件被订阅时 我们遍历堆栈并依次执行前面包装发布事件的函数 ps: 离线事件的生命周期只有一次

## 思考2-全局事件的命名冲突

全局的发布－订阅对象里只有一个clientList来存放消消名和回调函数，大家都通过它来订阅和发布各种消息，难免会出现事件名冲突的情况，所以我们需要给Event 提供一个创建命名空间的功能

## 总结

发布订阅模式的优点非常明显

- 时间上的解藕
- 对象之间的解藕 它的应用非常广泛，可以用在async编程当中，也可以帮助我们更松藕合的代码编写 当然也不是完全没有缺点，创建订阅者本身要消耗一定的时间和内存，而且当你订阅一个消息后，也许此消息最后都没有发生，但这个订阅者会始终存在内存当中，所以不建议过度使用 到这里我们的发布订阅模式就已经差不多了，现在我们使用它来构建一个我们的事件库[subdata]()
