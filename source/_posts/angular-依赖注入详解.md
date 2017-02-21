---
title: angular 依赖注入详解
date: 2017-02-20 16:33:16
tags: js,angular
description: 在2年前就在工作中接触angular，以前写的博客也在迁移中而丢失，最近公司有个需求是基于阿里开源的一个项目完成的，恰巧也是angular ， 那么趁这个机会把这个再温习一次
---

# 依赖注入是什么 ？

首先我们用大白话来理解一次，依赖注入就是我有一个东西，我平时不用，我把这个东西放在你那里，在我用的时候你拿给我。

这个时候我们已经初步有了概念，我们在用代码来消化这个概念

首先我们来先创建一个`inject`的对象，对象下有个三个属性分别为
- `dependencies`这个为一个数组，用来存放我们所有的依赖
- `register` 这个为一个函数，用来注册我们的依赖
- `resolve` 这个用来注入（提取）我们的依赖

```js
const inject = {
  dependencies: {},
  register: function(key, value) {
    this.dependencies[key] = value;
  },
  resolve: function(deps, func, scope){
    let arr = [];
    for (let i = 0 ; i < deps.length ; i++) {
         if (this.dependencies.hasOwnProperty(deps[i])) {
            arr.push(this.dependencies[deps[i]])
         }
    }
    return function(){
      func.apply(scope||{},arr);
    }
  },
}
```

嗯， 大概就是这个，即然已经把初步的想法实现，我们现在就来试试到底可不可以注入

首先实现我有一个东西，并且把这个东西存在你那，那么我们用`register`方法来注册两个

```js
inject.register('$http', {
  'get': function() {
    return '我是依赖注入的$http下的一个函数';
  }
});
inject.register('$scope', {
  'test': ''
});
```

这个时候我需要用到我前面存放在你那里东西，你得拿给我

```js
let MyController = function($scope,$http){
  console.log(`MyController-result:${$http.get()}`)
}

MyController = inject.resolve(['$scope','$http'],MyController)
MyController(); // MyController-result:我是依赖注入的$http下的一个函数

```

嗯，完美，这样就简单的实现我们的注入，但我们仔细一下，如果这个时候我们把这个依赖注入的顺序变换一下是报错的，因为他们是一一对应的，所以我们还得来改造一下`resolve`这个方法，

```js
resolve: function(func, scope){
  let arr = [];
  // 这些正则是在angular的源码里找来的
  // 首先把这个函数toString,然后FN_ARGS匹配出来函数的参数，这个参数就是我们依赖的表
  // 那么这个时候我们已经拿到依赖的表了，我们按照这个表里取出相对应的函数体给他就完了
  let FN_ARGS = /^function\s*[^\(]*\(\s*([^\)]*)\)/m;
  let STRIP_COMMENTS = /((\/\/.*$)|(\/\*[\s\S]*?\*\/))/mg;
  let fnText = func.toString().replace(STRIP_COMMENTS, '');
  let argDecl = fnText.match(FN_ARGS);
  let deps = argDecl[1].split(',');
  for (let i = 0 ; i < deps.length ; i++) {
       if (this.dependencies.hasOwnProperty(deps[i])) {
          arr.push(this.dependencies[deps[i]])
       }
  }
  return function(){
    func.apply(scope||{},arr);
  }
}
```

当然使用的时候也得改一下
```js
let MyController = function($scope,$http){
  console.log(`MyController-result:${$http.get()}`)
}

MyController = inject.resolve(MyController)
MyController(); // MyController-result:我是依赖注入的$http下的一个函数
```

看上去有点像模样了，这时候我觉得应该有人会提出问题，嗯，对，就是还有问题，我们知道上线前我们一般会把项目静态资源打包，`function($scope,$http)`会打成类似于`function(a,b)`这种，那么这个时候还怎么去打到对应的函数体呢 ？ 我们再来改写一下`resolve`方法

```js
resolve: function(func, scope) {
  let deps;
  // 如果传入的函数是一个数组，大概长这样[a,b,function(a,b){}]，那么我们删掉function(){}这部分，只留下我们的依赖部分,这样就解决了打包压缩时的问题
  if (isArray(func)) {
    let last = func.length - 1;
    deps = func.slice(0, last);
    func = func[last]
  } else {
    let FN_ARGS = /^function\s*[^\(]*\(\s*([^\)]*)\)/m;
    let STRIP_COMMENTS = /((\/\/.*$)|(\/\*[\s\S]*?\*\/))/mg;
    let fnText = func.toString().replace(STRIP_COMMENTS, '');
    let argDecl = fnText.match(FN_ARGS);
    deps = argDecl[1].split(',');
  }

  let arr = [];
  for (let i = 0; i < deps.length; i++) {
    if (this.dependencies.hasOwnProperty(deps[i])) {
      arr.push(this.dependencies[deps[i]])
    }
  }
  return function() {
    func.apply(scope || {}, arr);
  }
}
```

当然使用的时候还是得改改

```js
let MyController = ['$scope', '$http', function($scope, $http) {
  console.log(`MyController-result:${$http.get()}`)
}];
MyController = inject.resolve(MyController)
MyController(); // MyController-result:我是依赖注入的$http下的一个函数
```

这样我们一个简易的dependencies inject就完成了，angular的实现思路也是如此
