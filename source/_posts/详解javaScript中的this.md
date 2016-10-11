---
title: 详解javaScript中的this
date: 2016-10-11T16:27:32.000Z
tags: js this
description: JavaScript的指向总是指向一个对象，而具体指向哪个对象是在运行时基于函数的执行环境动态绑定的， 而非函数被声明时的环境具体到实际应用中，this 的指向大致可以分为以下几种
---
# this 的指向
JavaScript的指向总是指向一个对象，而具体指向哪个对象是在运行时基于函数的执行环境动态绑定的， 而非函数被声明时的环境<br>
具体到实际应用中，this 的指向大致可以分为以下几种

- 作为对象的方法调用
- 作为普通函数调用
- 构造函数调用
- call,apply 调用

# 作为对象的方法调用

```
var obj = {
  name : 'febobo',
  getName : function(){
    console.log(this===obj) // true
    console.log(this.name) // febobo
  }
}
obj.getName()
```

# 作为普通函数调用

```
var name = 'febobo',
getName = function(){
  console.log(this.name) // febobo
}
getName()
// 或者我们以下面这种方式能看的更明了一点
var obj = {
  name : 'febobo',
  getName : function(){
    console.log(this===obj) // false
    console.log(this.name) // undefined
  }
}
var getName =  obj.getName()
getName();
```

# 构造器调用

通常情况下，构造器里的this就指向返回的这个对象

```
var myClass = function(){
  this.name = 'febobo'
}
var obj = new myClass();
console.log(obj.name) // febobo
```

这里要说的是， 如果构造器显示的返回一个对象，那么运算结果最终会返回这个对象，而this也不是我们之前期待的this

```
var myClass = function(){
  this.name = 'febobo'
  return {
    name : '十三把刀'
  }
}
var obj = new myClass();
console.log(obj.name) // 十三把刀
```

如果构造器返的是一个非object类型的数据，那么就不会出现如上问题,比如我们返回是一个number

```
var myClass = function(){
  this.name = 'febobo'
  return 123 // 返回number 类型
}
var obj = new myClass();
console.log(obj.name) // febobo
```

# call,apply 调用

call和apply方法能很好的体现JavaScript的函数式语言特性，在JavaScript中，几乎每一次编写函数式语言风格的代码，都离不开call和apply， call和apply更多的应用场景是在设计模式中

- 跟普通的函数相比， call和apply可以动态的改变传入函数的this

```
var obj1 = {
  name : 'febobo',
  getName : function(){
    console.log(this.name)
  }
}

var obj2 = {
  name : '十三把刀'
}
console.log(obj.getName()) // febobo
console.log(obj.getName.call(obj2)) // 十三把刀
```

# 思考

熟悉js的人都使用document.getElementById这个方法， 有时我们为了方便就会把这个封装成一个函数

```
var getById = function(id){
  return document.getElementById(id)
}
getById('febobo')
// 这时候我们会想能不能想简单点比如下面这样
var getById = document.getElementById;
getById('febobo') // error
```

上面的代码会抛出一个错误，因为document.getElementById方法的实现中需要用到this,这个this是期望指向document的，而我们把document.getElementById的引用真接给了getById,然后就直接运行getById, 这样就成了普通函数调用，所以这时的this是指向window 的，所以会有上面的错误，现在我们来尝试用apply来改进一下代码

```
document.getElementById = (function(fn){
  return function(){
    return fn.apply(document,arguments)
  }
})(document.getElementById);
var getById = document.getElementById;
getById('febobo') // dom Array
// 上面我们通过apply成功修正了this 指向
```

## call种apply的区别

它们的作用一模一样，唯一的区别仅在于传入的参数型式不同 apply接收两个参数，第一个代表函数内的this的指向，第二个参数为一个带下标的集合（数组，类数组），apply方法把这个集合中的元素作用参数传递给调用的函数

```
var func = function(a,b,c){
  console.log(a,b,c);
}
func.apply(null,[1,2,3]) // 1,2,3
```

call传入的参数数量不固定，跟apply相同的是，第一个参数也是代表函数体内的this指向，从第二个参数开始，每个参数被依次传入

```
var func = function(a,b,c){
  console.log(a,b,c);
}
func.call(null,1,2,3) // 1,2,3
```

上面可以看到我们把函数体几的this指向了null,其实当我们使用call跟apply的时候传入的第一个参数为空，函数体内的指向会指向window,但如果是在严格模式下this还是为null

```
var func = function(a,b,c){
  console.log(this===window);
}
func.call(null,1,2,3) // true

var func = function(a,b,c){
  "use strict"
  console.log(this===null);
}
func.call(null,1,2,3) // true
```

## call的apply的用途

- 改变this指向(具体见上方代码)
- Function.prototype.bind
- 借用其它对象方法

## Function.prototype.bind

高级浏览器都内置了Function.prototype.bind,用来指定函数内部的this指向,下面我们可以来模拟一个bind

```
Function.prototype.bind = function(content){
  var _this = this; // 保存原函数指向
  return function(){
    return _this.apply(content,arguments)
  }
}
var obj = {
  name : 'febobo'
}
var func = function(){
  console.log(this.name)
}.bind(obj)
func() // febobo
```

## 借用其它对象方法

```
var a = {};
Array.prototype.push.call(a,'febobo')
```
