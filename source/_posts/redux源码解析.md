---
title: redux源码解析
date: 2017-06-07 17:43:13
tags: redux,js
description: redux深入之源码解析
---
## 写在最前面

在深入`redux`源码之前，我们还是要对redux有一个基础的认识

## What is redux

[Redux](https://github.com/reactjs/redux) 是应用状态管理服务。虽然本身受到了 Flux 很深的影响，但是其核心概念却非常简单，就是 `Map/Reduce` 中的 `Reduce`

我们简单的来了解一下`js`中的`reduce`

```
const initState = '';
const actions = ['a', 'b', 'c'];
const newState = actions.reduce(
    ( (prevState, action) => prevState + action ),
    initState
);
```

从 Redux 的角度来看，应用程序的状态类似于上面函数中的 initState 和 newState 。给定 initState 之后，随着 action 的值不断传入给计算函数，得到新的 newState。

## Why use redux

每个 web 应用都至少对应一个数据结构，而导致这个数据结构状态更新的来源很丰富；光是用户对 view 的操作(dom 事件)就有几十种，此外还有 ajax 获取数据、路由/hash状态变化的记录和跟踪等。

来源丰富不是最可怕的，更可怕的是每个来源提供的数据结构并不统一。DOM 事件还好，前端可以自主控制与设计； ajax 获取的数据，其结构常常是服务端开发人员说了算，他们面对的业务场景跟前端并不相同，他们往往会为了自己的便利，给出在前端看来很随意的数据结构。

由此，管理一个应用的 state 也变得非常困难，redux 就是用来统一管理应用的state，redux 试图让 state 的变化变得可预测,下面我们用一张图来了解一下redux 是如何工作的

![redux 工作路径](https://user-images.githubusercontent.com/9276376/26873058-2c8e9f34-4bab-11e7-889c-bf1450665702.png)


## 进入主题

redux 为我们提供了核心的api，首先我们把它们都列出来，再一一进行源码分析

- createStore
- store
- combineReducers
- applyMiddleware
- bindActionCreators
- compose

### createStore

[createStore源码分析](http://zhangbo.news/2017/06/07/redux%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90-createStore/)

### store

Store 就是用来维持应用所有的 state 树 的一个对象。 改变 store 内 state 的惟一途径是对它 dispatch 一个 action

当我们将应用的根reducing函数传递给createStore时就会创建一个store

#### store的方法
- getState()
  获取当前的state,这个很简单，一目了解，下面是[源码实现](https://github.com/reactjs/redux/blob/master/src/createStore.js#L74)
  ```
  function getState() {
    return currentState
  }
  ```

- dispatch(action)
  分发 action。这也是触发 state 变化的惟一途径,[源码实现](https://github.com/reactjs/redux/blob/master/src/createStore.js#L149)
  ```
  try {
    isDispatching = true
    // 通过action 来 改变currentState
    currentState = currentReducer(currentState, action)
  } finally {
    isDispatching = false
  }
  // 每次dispatch 都会触发subscribe 过的listeners
  const listeners = currentListeners = nextListeners
  for (let i = 0; i < listeners.length; i++) {
    const listener = listeners[i]
    listener()
  }
  ```

- subscribe(listener)
  添加一个变化监听器。每当 dispatch action 的时候就会执行，state 树中的一部分可能已经变化。你可以在回调函数里调用 getState() 来拿到当前 state,[源码实现](https://github.com/reactjs/redux/blob/master/src/createStore.js#L101)
  ```
  let isSubscribed = true
  //  在每次 dispatch() 调用之前都会保存一份快照。当你在正在调用监听器（listener）的时候订阅(subscribe)或者去掉订阅（unsubscribe），对当前的 dispatch() 不会有任何影响。但是对于下一次的 dispatch()，无论嵌套与否，都会使用订阅列表里最近的一次快照
  ensureCanMutateNextListeners()
  nextListeners.push(listener)

  return function unsubscribe() {
    if (!isSubscribed) {
      return
    }

    isSubscribed = false

    ensureCanMutateNextListeners()
    const index = nextListeners.indexOf(listener)
    nextListeners.splice(index, 1)
  }
  ```

### combineReducers(...reducer)

combineReducers 辅助函数的作用是，把一个由多个不同 reducer 函数作为 value 的 object，合并成一个最终的 reducer 函数，然后就可以对这个 reducer 调用 createStore

最终，state 对象的结构会是这样的：
```
{
  reducer1: ...
  reducer2: ...
}
```

[源码实现](https://github.com/reactjs/redux/blob/master/src/combineReducers.js#L102)
```
return function combination(state = {}, action) {
  if (shapeAssertionError) {
    throw shapeAssertionError
  }

  if (process.env.NODE_ENV !== 'production') {
    const warningMessage = getUnexpectedStateShapeWarningMessage(state, finalReducers, action, unexpectedKeyCache)
    if (warningMessage) {
      warning(warningMessage)
    }
  }

  let hasChanged = false
  const nextState = {}
  for (let i = 0; i < finalReducerKeys.length; i++) {
    const key = finalReducerKeys[i]
    const reducer = finalReducers[key]
    // 上一次key的state
    const previousStateForKey = state[key]
    // 通过传入上一次的state跟action给reducer函数，返回新的state
    const nextStateForKey = reducer(previousStateForKey, action)
    if (typeof nextStateForKey === 'undefined') {
      const errorMessage = getUndefinedStateErrorMessage(key, action)
      throw new Error(errorMessage)
    }
    nextState[key] = nextStateForKey
    // 判断是否改变，改变返回nextState , 没有则返回上一次的state
    hasChanged = hasChanged || nextStateForKey !== previousStateForKey
  }
  return hasChanged ? nextState : state
}
```

### applyMiddleware(...middleware)

Middleware 可以让你包装 store 的 dispatch 方法来达到你想要的目的。同时， middleware 还拥有“可组合”这一关键特性。多个 middleware 可以被组合到一起使用，形成 middleware 链。其中，每个 middleware 都不需要关心链中它前后的 middleware 的任何信息。

简单使用
```
function logger({ getState }) {
  return (next) => (action) => {
    console.log('will dispatch', action)

    // 调用 middleware 链中下一个 middleware 的 dispatch。
    let returnValue = next(action)

    console.log('state after dispatch', getState())

    // 一般会是 action 本身，除非
    // 后面的 middleware 修改了它。
    return returnValue
  }
}

let store = createStore(
  todos,
  [ 'Use Redux' ],
  applyMiddleware(logger)
)
```
[源码实现](https://github.com/reactjs/redux/blob/master/src/applyMiddleware.js)

```
export default function applyMiddleware(...middlewares) {
  return (createStore) => (reducer, preloadedState, enhancer) => {

    // 这里我们在介绍createStore时讲过， 使用createStore传入enhancer时会直接返回enhancer过的store
    const store = createStore(reducer, preloadedState, enhancer)
    let dispatch = store.dispatch
    let chain = []

    const middlewareAPI = {
      getState: store.getState,
      dispatch: (action) => dispatch(action)
    }

    // 给每个middleware增加middlewareAPI
    chain = middlewares.map(middleware => middleware(middlewareAPI))

    // 从右于左调用middleware
    dispatch = compose(...chain)(store.dispatch)

    // 返回包装后的dispatch
    return {
      ...store,
      dispatch
    }
  }
}
```


### bindActionCreators(actionCreators, dispatch)

惟一使用 bindActionCreators 的场景是当你需要把 action creator 往下传到一个组件上，却不想让这个组件觉察到 Redux 的存在，而且不希望把 Redux store 或 dispatch 传给它

使用示例
```
// TodoActionCreators.js

export function addTodo(text) {
  return {
    type: 'ADD_TODO',
    text
  };
}

// SomeComponent.js

import { Component } from 'react';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

import * as TodoActionCreators from './TodoActionCreators';
console.log(TodoActionCreators);
// {
//   addTodo: Function,
// }

class TodoListContainer extends Component {
  componentDidMount() {
    // 由 react-redux 注入：
    let { dispatch } = this.props;

    // 注意：这样做行不通：
    // TodoActionCreators.addTodo('Use Redux');

    // 你只是调用了创建 action 的方法。
    // 你必须要 dispatch action 而已。

    // 这样做行得通：
    let action = TodoActionCreators.addTodo('Use Redux');
    dispatch(action);
  }

  render() {
    // 由 react-redux 注入：
    let { todos, dispatch } = this.props;

    // 这是应用 bindActionCreators 比较好的场景：
    // 在子组件里，可以完全不知道 Redux 的存在。

    let boundActionCreators = bindActionCreators(TodoActionCreators, dispatch);
    console.log(boundActionCreators);
    // {
    //   addTodo: Function,
    // }

    return (
      <TodoList todos={todos}
                {...boundActionCreators} />
    );
    // return <TodoList todos={todos} dispatch={dispatch} />;
  }
}

export default connect(
  state => ({ todos: state.todos })
)(TodoListContainer)
```


[源码实现](https://github.com/reactjs/redux/blob/master/src/bindActionCreators.js)

```
// 核心很简单，使用dispatch包装一下原action
function bindActionCreator(actionCreator, dispatch) {
  return (...args) => dispatch(actionCreator(...args))
}
```

## 结语

下一章节将进行react-redux源码解析
