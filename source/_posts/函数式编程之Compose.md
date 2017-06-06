---
title: 函数式编程之Compose
date: 2017-06-06 10:51:58
tags:
description: 有些事物在你得到之前是无足轻重的，得到之后就不可或缺了。微波炉是这样，智能手机是这样，互联网也是这样——老人们在没有互联网的时候过得也很充实。对函数式编程来说，函数的Compose(组合)也是这样
---



## Compose基本概念

顾名思义，在函数式编程中，`Compose` 就是将几个有特点的函数拼凑在一起， 让它们结合， 产生一个崭新的函数，如下就是组合

```
const compose = (f,g) => (...arg) => f(g(...arg))
```

`f`跟`g`在如上都是函数，`...arg`是在他们之间通过管道(pipe)传输的值


让我们来尝试使用一下组合

```
let toUpperCase = (x) => x.toUpperCase();
let exclaim = (x) => x + '!';
let shout = compose(toUpperCase,exclaim);
shout('hello world')
// HEELO WORLD !
```

## pointfree 模式

pointfree模式指的是，永远不必说出你的数据，它的意思是指函数无须提及将要操作的数据是什么样的，一等公民的函数，curry 以及compose 协作起来非常有助于实现这种模式

```
// 非 pointfree, 因为提到了数据 word

let snakeCase = (word) => word.toUpperCase().replace(/\s+/ig,'-');

// pointfree

let snakeCase = compose(replace(/\s+/ig,'-'),toUpperCase)

```

`pointfree` 模式能够帮助我们减少不必要的命名，让代码保持简洁和通用。对函数式代码来说，`pointfree` 是非常好的石蕊试验，因为它能告诉我们一个函数是否是接受输入返回输出的小函数。


## 一些问题

回到楼上，我们可以看到上面的compose示例都只是传入了两个函数，因为我们的compose实现也只支持两个函数，那么如果我们想要支持一条很长很长的管道的时候，显然上面的compose就不够用了，下面我们可以来看看[redux](https://github.com/reactjs/redux/blob/master/src/compose.js)是如何实现compose的


```
// 摘自 https://github.com/reactjs/redux/blob/master/src/compose.js

export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }

  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```

代码很简洁，利用了数组的reduce方法来处理，就这样， 想拼多长就多长
