---
title: JS设计模式-职责链模式
date: 2016-10-11T16:24:16.000Z
tags: null
---

# 定义

使多个对象都有机会处理请求，避免请求的发送者与接收者之间的耦合关系，将这些对象形成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止

# 现实中的职责链模式

早高峰挤公交车，往往挤上去却看不到售票员，我们常常通过他人之手将票钱传递给售票员，这种关系就能看做为职责链，我们的票钱通过多人之手最终递交到售票员手中

# 实际中的职责链模式

假设我们正在开发一个电商网站，某一个商品正在进行预定活动，活动规则如下

- 500 元定金会收到200 优惠劵
- 200 元定金会收到100 优惠劵
- 没有预定的用户只能普通购买

假设我们后端会返回如下字段

- orderType [1,2,3] 分别为500,200,普通购买

接下来我们用代码实现

```
var order = function(orderType){
  if(orderType===1){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(500元定金用户)

  }
  if(orderType===2){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(200元定金用户)
  }
  if(orderType===3){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(用户普通购买)
  }
}

order(1) // 500元定金用户
```

虽然我们得到了意料中的运行结果，但这并不是一段优秀的代码，order函数会随着业务的变更经常修改

# 职责链重构order函数

我们把500,200,普通购买抽离成单独的函数，如果该函数不符合处理条件，则把请求扔给下一个函数，直到符合条件为止

```
var ordre500 = function(orderType){
  if(orderType===1){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(500元定金用户)
  }
  order200(orderType)  // 如果不符合处理条件，则将请求扔给下一个函数处理
}
var ordre200 = function(orderType){
  if(orderType===2){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(200元定金用户)
  }
  ordreNormal(orderType)
}
var ordreNormal = function(orderType){
  if(orderType===3){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(用户普通购买)
  }
}
// 测试
ordre500(1) // 500元定金用户
ordre500(2) // 200元定金用户
```

可以看到我们把一个大函数拆分成了3个小函数，去掉了多层嵌套的if语语，但可以看到请求在链条传递的顺序非常生硬，传递请求的代码耦合在了业务函数

```
var ordre500 = function(orderType){
  if(orderType===1){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(500元定金用户)
  }
  order200(orderType)  // 耦合代码
}
```

显然这是违反开放－封闭原则的，如果有我们要将200改成300, 那就意味着必须的去改动这些业务函数内部

# 灵活可拆分的职责链

我们采用一种更灵活的方式，来改进上面职责链模式，目的是让链中的各个节点可以灵活拆分重组<br>
首先我们需要改写3种购买方式的节点函数，我们约定，如果某个节点不能处理请求则返回一个false 来表示该请求需要继续往后传递

```
var ordre500 = function(orderType){
  if(orderType===1){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(500元定金用户)
  }
  return false  // 如果不符合处理条件，则将请求扔给下一个函数处理
}
var ordre200 = function(orderType){
  if(orderType===2){
    // 假设我们有其它需求
    if(....){
      .....66
    }
    return console.log(200元定金用户)
  }
  return false
}
var ordreNormal = function(orderType){
  if(orderType===3){
    // 假设我们有其它需求
    if(....){
      .....
    }
    return console.log(用户普通购买)
  }
  return false
}
```

接下来需要把函数包装进职责链节点，我们定义一个构造函数Chain,在new Chain 的时候传递的参数即为需要被包装的函数，同时它还拥有一个实例属性this.successor ， 表示在链中的下一个节点

```
var Chain = function(fn){
  this.fn = fn;
  this.successor = null;
}

Chain.prototype.setNextSuccessor = function(successor){ 指定在链中的下一个节点
  return this.successor = successor;
}

Chain.prototype.passRequest = function(){  //传递请求给某一下节点
  var ret = this.fn.apply(this,arguments);
  if(ret === false){  // 如果ret 为false 代表链条还得继续往下走
    return this.successor && this.successor.passRequest.apply(this.successor,arguments)
  }
}

// 包装成职责链的节点
var chainOrder500 = new Chain(order500);
var chainOrder200 = new Chain(order200);

// 然后再指定节点在链中的顺序
chainOrder500.setNextSuccessor(chainOrder200)

// 最后把请求传递给第一个节点
chainOrder500.passRequest(1)  // 500元定金用户
```

通过上面的改进，我们可以灵活的增加，移除链中的节点顺序，假设哪天产品大喊着要加个300减100的活动，我们只需要增加一个节点即可

```
var order300 = function(){
  ....// 假装有代码
}
```

# 异步的职责链

很多时候我们在现实开发当中，经常会遇到一些异步的问题，比如我们要在节点函数中发起一个ajax 请求，等到ajax 请求回来以后才决定是否继续在职责链中passRequest,这个时候我们同步返回一个false 已经没有意义了， 所以要给原型增加一个手动passRequest ,接下来我们给Chain 增加一个next 方法，表示手动传递请求到职责链中下一个节点

```
Chain.prototype.next(){
  return this.successor && this.successor.passRequest.apply(this.successor,arguments)
}

var fn1 = function(){
  var _this = this;
  setTimeout(function(){
    console.log(1);
    _this.next()
  },1000)
}
var fn2 = function(){
  console.log(2);
  return false
}

var fn1Chain = new Chain(fn1);
var fn2Chain = new Chain(fn2);

fn1Chain.setNextSuccessor(fn2Chain); // 1,2
```

# 用AOP实现职责链

用AOP实现职责链又简单又巧妙，但这种函数叠加在一起的方式，同时也叠加了函数的作用域，如果链条太长也会对性能造成影响

```
Function.prototype.after(fn){
  var _this = this;
  return function(){
    var ret = _this.apply(this,arguments);
    if(ret === false){
      return fn.apply(this.arguments);
    }
    return ret
  }
}
var order = order500.after(order200).after(orderNormal);
order(2) // 200定金用户
```

# 总结

职责链模式可以很好的帮助我们管理代码，降低发起请求的对象跟接收请求对象的耦合，职责链中的节点顺序是可变化的，我们可以在运行中决定链中包含哪些节点
