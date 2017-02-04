---
title: React－那些你可能会踩到过坑
date: 2016-11-23 17:37:03
tags:
description: 最近在做一个运营配置项目，想着把所有组件都放进一个对象里，然后通过JSON中的type来取相对应的模板
代码大概是下面这样子的
---

### 大写的尴尬😅
最近在做一个运营配置项目，想着把所有组件都放进一个对象里，然后通过JSON中的type来取相对应的模板

代码大概是下面这样子的
```js
const customModule = pageModules[v.type];
const data = {
  ...v,
  key: k,
  tag: v.ref,
  preview: () => {
    setTimeout( ()=> {
      this.onSubmit('',v.ref)
    },100)
  }
}
return (
  <customModule
    {...data}
  />
)
```
以为大功告成了么 ，并没有，也没有任何错误信息，`console.log(customModule)`出来看一下，发现typeof为 react Element 没错，但是发现了它的type 竟然是个string 类型，这就尴尬了，去翻了下官网找到如下一段话

```
You cannot use a general expression as the React element type. If you do want to use a general expression to indicate the type of the element, just assign it to a capitalized variable first. This often comes up when you want to render a different component based on a prop:
// Correct! JSX type can be a capitalized variable.
```
对，就是这样，，，， 把`customModule`改为`CustomModule`就ok了。
