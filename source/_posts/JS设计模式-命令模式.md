---
title: JS设计模式-命令模式
date: 2016-10-11T16:25:07.000Z
tags: null
categories: JS设计模式
description: 有时候需要向某些对象发送请求， 但是并不知道请求的接收者是谁， 也不知道请求的操作是什么， 些时希望用一种低耦合的方式来设计程序，使得请求者和发送者之间的关系低耦合,下面我们来看一个例子
---

命令模式是最简单跟优雅的模式之一，命令(command)指的是一个执行某些特定事情的指令

# 应用场景

有时候需要向某些对象发送请求， 但是并不知道请求的接收者是谁， 也不知道请求的操作是什么， 些时希望用一种低耦合的方式来设计程序，使得请求者和发送者之间的关系低耦合,下面我们来看一个例子<br>
比如现在我们正在写一个大型用户界面程序，该用户界面上至少有数十个button按钮，因为项目比较复杂，所以我们同时让多人合作开发这个系统，A负责编写静态界面，B负责点击按钮的具体行为，这些行为都将被封装到对象里

- 思考 B完全不知道点击完这个按钮后会发生什么事情，那么如何给它绑定click事件呢 ？<br>
  现在我们回头来看一下应用场景，找到了使用命令模式的理由，下面我们用代码来实现一下 ``` // 首先我们定义一个安装命令的方法 var setCommand = function(button,command){ button.onclick = function(){ command.execute(); } } // 定义最终执行者 var MenuBar = { refresh : function(){ console.log('refresh menu finish') } }

// 在让button 变得有用起来之前， 我们要先把这些行为封装在命令类中 var RefreshMenuBarCommand = function(recevier){ this.recevier = recevier; }

RefreshMenuBarCommand.prototype.execute = function(){ this.recevier.refresh(); }

// 最后就是把命令接收者传入到command对象中， 并且把command对象安装到button 上面

var refreshCommand = new RefreshMenuBarCommand(MenuBar);

setCommand('button1',refreshCommand)

```

通过上面的代码我们能看出来， 接收者跟执行者完全没有任何联系，通过了一个setCommand命令结合到了一块，这样发送者跟接收者就被耦合开了

## JavaScript 中的命令模式
JavaScript将函数作为一等对象的语言，跟策略模式一样，命令模式也早已融入到了JavaScript语言当中，运算块不一定要封装在command.execute方法中， 也可以封装在普通函数中,函数作为一等对象，本身就可以被可以被四处传递，即使我们依然需要请求接收者，那也未必使用面向对象的方式，闭包也可以完成同样的功能
```

var setCommand = function(button,fn){ button.onclick = function(){ fn(); } }

var MenuBar = { refresh : function(){ console.log('refresh menu finish') } }

RefreshMenuBarCommand.prototype.execute = function(){ return function(){ recevier.refresh(); } }

var refreshCommand = new RefreshMenuBarCommand(MenuBar); setCommand('button1',refreshCommand)

```
## 撤消重做
命令模式的作用不仅是封装运算块，而且可以很方便的给命令对象增加撤消操作
比如我们有一个场景，下方我们用伪代码实现
```

<div id="moveWrap">moveWrap</div>

<input type="text">



<button>click me</button>

```
当我们在input里面输入一个值时，click button 我们的moveWrap会运动到input 指定的值
```

var MoveCommand = function(recevier , pos){ this.recevier = recevier; this.pos = pos; } MoveCommand.prototype.execute = function(){ this.recevier.move('left',this.pos) } var movaCommand = new MoveCommand()

button.onclick = function(){ movaCommand.execute(); }

```
如果我们想在moveWrap移动到一个指定的位置的后回到原到的位置，如果目前这个场景来看， 我们是可以在input 里再输入moveWrap原来的坐标是可以回到原来的位置的，不过显然不是我们想要的，下面我们为MoveCommand增加一个撤消的功能
撤消操作的实现一般是给命令对象增加一个unexecude或者undo的方法,在该方法里执行execute的反向操作，如果我们想实现moveWrap回到原来的位置， 那么我们只需要在moveWrap 移动的时候记录一下当前位置，然后在unexecude或者undo 方法让moveWrap回到在前面记录的位置即可,下面我们改写代码
```

MoveCommand.prototype.execute = function(){ this.recevier.move('left',this.pos); this.oldPos = this.recevier.dom.getBoundingClientRect()[this.recevier.propertyName] }

MovaCommand.prototype.undo = function(){ this.recevier.move('left',this.oldPos) } ``` 撤消是命令模式里一个非常有用的功能，比如在开发一个象棋的时候，悔棋操作用命令模式来做非常方便，又比如各种ctrl+z这种撤消操作
