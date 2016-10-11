---
title: JS设计模式-中介者模式
date: 2016-10-11T16:23:38.000Z
tags: null
---

# 中介者模式

中介者模式的作用就是解除对象与对象之间的紧耦合关系，增加一个中介者对象后，所有的相关对象都通过中介者对象来进行通信，而不是相互引用，所以当一个对象发生改变时，只需要通知中介者对象即可，中介者使各对象耦合松散，而且可以独立的改变它们之的交互，中介者模式使多对多关系变成了相对简单的一对多关系

## 现实中的中介者

中介者也被称为调停者以，我们想象一下机场的指挥塔，如果没有指挥塔的存在，每一架飞机要和方圆百公里的所有飞机通信，才能确认航线以及飞行情况，现实中的情况是，每架飞机只需要跟指挥塔通信，指挥塔作为调停者，知道每一架飞机的飞行情况，所以它可以安排所有飞机的起降时间，及时做出航线调整

## 开发中的中介者

假设我们正在编写一个实时的表单验证的程序，有如下需求

- 当用户名未输入时输入密码将提示先输入用户名
- 当密码未输入时输入确认密码将提示先输入密码
- 当性别未选择时输入年龄将提示先选择性别
- 当点击提交按钮的时候如果有其它项未项写就提示当前项不能为空 我们的实现代码如下

  ```
  var nameInput = document.getElementById('nameInput'),
    passwordInput = document.getElementById('nameInput'),
    repeatPasswordInput = document.getElementById('nameInput'),
    sexInput = document.getElementById('nameInput'),
    ageInput = document.getElementById('ageInput');

    passwordInput.onchange = function(){
      if(!nameInput.value){
        return alert('请先输入用户名')
      }
    }
    repeatPasswordInput.onchange = function(){
      if(!nameInput.value){
        return alert('请先输入密码')
      }
    }
    ageInputInput.onchange = function(){
      if(!sexInput.value){
        return alert('请先选择性别')
      }
    }

    submit.onclick = function(){
      if(!nameInput.value){
        return alert('请先输入用户名')
      }
      if(!nameInput.value){
        return alert('请先输入密码')
      }
      if(!sexInput.value){
        return alert('请先选择性别')
      }

      ajax(...)
    }
  ```

  ## 引入中介者

  虽然目前顺利完成了编码，但随之来的需求改变可能给我们带来麻烦，现在这个验证的节点还不算多，如果增加到10个或者更多，它们的关系可能变的错综复杂，现在我们来引入中介者对象，所有的节点验证都只跟中介者通信，这样一来，无论是修改还是新增验证节点，我们都只需要改变中介者对象里的代码<br>

  ```<br>
  var mediator = (function(){ var nameInput = document.getElementById('nameInput'),

  passwordInput = document.getElementById('nameInput'), repeatPasswordInput = document.getElementById('nameInput'), sexInput = document.getElementById('nameInput'), ageInput = document.getElementById('ageInput');

  return { changed : function(obj){

  if(obj === passwordInput && !nameInput.value){<br>
  return alert('请先输入用户名')<br>
  }<br>
  if(obj === repeatPasswordInput && !passwordInput.value){ return alert('请先输入密码') }<br>
  if(obj === ageInputInput && !sexInput.value){ return alert('请先选择性别') }

  } } })()

// 我们只需要通知中介者<br>
passwordInput.onchange = function(){<br>
mediator.changed(this)<br>
}<br>
repeatPasswordInput.onchange = function(){<br>
mediator.changed(this)<br>
} ageInputInput.onchange = function(){ mediator.changed(this) }

// 后面如果再增加其它节点验证我们只要需改动mediator对象就好<br>
var mediator = (function(){ / .... / return { changed : function(obj){ / .... / if(obj === homeInputInput && !cityInput.value){ return alert('请先选择城市') } } } })() ```

## 小结

中介者模式用大白话翻译指的就是一个对象应该尽可能的了解另一个对象（类似不和陌生人说话）,如果对象之间的耦合性太高，一个对象发生改变之后，难免会影响到其它的对象，跟城门失火，殃及池鱼的道理是一样的，而在中介者模式里，对象之间几乎是不知道彼此存在的，它们只能通过中介者对象来相互影响
