---
title: angular 双向绑定详解
date: 2017-02-21 10:35:43
tags: js,angular
description: angular前几年之所以这么受欢迎，是离不开双向绑定的这个特性的，那么双向绑定后面的神秘面纱到底是什么样子的，让我们一起用手来撕开*_*
---

#MVC
开始之前，我们先来温习一下MVC设计模式,即`model,controller,view`

* `view` 层传送指令至`controller`
* `controller`完成逻辑后，要求`model`层改变数据
* `model`将新的数据传送至`view`，用户得到反溃

所有的数据流向都是单向的，我们用一张图可能理解的更清楚一点
![image](https://cloud.githubusercontent.com/assets/9276376/23148824/d2344330-f822-11e6-8d3e-872d778d9520.png)

那么为什么开始之前要讲一下这个`MVC`呢？有同学会说，因为`angular`就是`mvc`模式，其实不然，`angular`更像是`mvvm`模式，从`mvc`后衍生了很多其它类似的模式，经典的有`mvp` ,`mvvm`

# MVVM

* model
* view
* viewModel

在`angular`中`viewModel` 就是`$scope`,我们还是用一张图来看一眼`mvvm`的数据是怎样流动的

![image](https://cloud.githubusercontent.com/assets/9276376/23148942/aa54e31e-f823-11e6-9e28-1b11da714e38.png)

在图中我们可以看出，`viewModel`跟`view`中的数据流是双向的，

# 基本概念

老规矩,我们还是用大白话来讲概念，在这里我就用一个词来形容就是同富贵也共患难，你怎么样，我就怎么样，打个比喻，你一直在买彩票，然后我对你说，你买彩票中了500万要请我吃一顿饭，然后你中了，你就请我吃了一顿饭，，，

# 实现原理
从上面的大白话，我们得到以下几条信息
* 一个承诺
* 一个兑现

嗯，其实就是这样，就是我们的订阅模式，我订阅了一个事件，你发布了一个事件，我得到反溃，现在我们在用代码来消化一下

```js
function Scope() {
  // 事件池
  this.$$watchers = [];
}

/*
  watchFn 监听谁
  listenerFn 监听后的反溃
 */
Scope.prototype.$watch = function(watchFn, listenerFn) {
  let watcher = {
    watchFn: watchFn,
    listenerFn: listenerFn
  };
  this.$$watchers.push(watcher);
}

Scope.prototype.$digest = function() {

  this.$$watchers.forEach(function(watcher) {
    var newVal = watcher.watchFn();
    var oldVal = watcher.last;
    if (newVal !== oldVal) {
      watcher.listenerFn(newVal, oldVal);
    }
    watcher.last = newVal;
  })
}
```

如上代码，实现我们的基本的思路，接下来我们来检验一下我们的代码功效

```js
let $scope = new Scope();

$scope.name = 'letv';
$scope.$watch(function() {
  return $scope.name;
}, function(newValue, oldValue) {
  console.log(`我是通过digest改变的值:${newValue}\n我是通过digest改变前的值:${oldValue}`);
  element[0].value = $scope.name;
});

// 伪代码，假设我们有一个input
let element = document.querySelectorAll('input');
element[0].onkeyup = function() {
  $scope.name = element[0].value;
  $scope.$digest();
};
```

嗯,结果是可行的，在`angular`中实现双向绑定正是订阅（观查者）模式,咱们这个只是缕缕思路，`angular`比我们的实现要复杂的多，比如在监听a的时候改变b，在监听b的时候又改变了a，这时候就会造成死循环，`angular`中使用`TTL`来解决这个问题的，当然还有很多，有兴趣的去翻翻源码吧
