---
title: Redux基础
date: 2020-09-26 16:57:53
categories:
- react
---
#### 考虑使用Redux的情景

1. 某个组件的状态，需要共享
2. 某个状态需要在任何地方都可以拿到
3. 一个组件需要改变全局状态
4. 一个组件需要改变另一个组件的状态

<p>Redux 的设计思想</p>

1. Web应用是一个状态机，视图与状态是一一对应的。
2. 所有的状态，保存在一个对象里面。

#### createStore

<p>Redux 提供<b>createStore</b>这个函数，用来生成 Store</p>

```js
import { createStore } from 'redux';
const store = createStore(reducer);
```
<!--more-->
<p>createStore函数接受另一个函数作为参数，返回新生成的 Store 对象</p>

#### State

<p>如果想得到某个时点的数据，就要对 Store 生成快照。这种时点的数据集合，就叫做 State。
当前时刻的 State，可以通过store.getState()拿到</p>

```js
const state = store.getState()
```

<p>Redux 规定， 一个 State 对应一个 View。只要 State 相同，View 就相同。你知道 State，就知道 View 是什么样，反之亦然。</p>

#### Action

<p>State 的变化必须是 View 导致的。Action 就是 View 发出的通知，表示 State 应该要发生变化了。
Action 是一个对象。其中的type属性是必须的，表示 Action 的名称。其他属性可以自由设置</p>

```js
const action = {
  type: 'ADD_TODO',
  payload: 'Learn Redux'
}
```

#### store.dispatch()

<p>store.dispatch()是 View 发出 Action 的唯一方法;接受一个 Action 对象作为参数，将它发送出去</p>

```js
import { createStore } from 'redux';
const store = createStore(reducer);

store.dispatch({
  type: 'ADD_TODO',
  payload: 'Learn Redux'
}
```

#### Reducer

<p>Store 收到 Action 以后，必须给出一个新的 State，这样 View 才会发生变化。这种 State 的计算过程就叫做 Reducer。</p>

<p>Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State。</p>

```js
const reducer = function (state, action) {
  // ...
  return new_state;
}
```

<p>实际应用中，Reducer 函数不用像上面这样手动调用，store.dispatch方法会触发 Reducer 的自动执行。为此，Store 需要知道 Reducer 函数，做法就是在生成 Store 的时候，将 Reducer 传入createStore方法。</p>

```js
import { createStore } from 'redux';
const store = createStore(reducer);
```

<p>Reducer 函数最重要的特征是，它是一个纯函数。纯函数是函数式编程的概念，必须遵守以下一些约束。</p>

1. 不得改写参数
2. 不能调用系统 I/O 的API
3. 不能调用Date.now()或者Math.random()等不纯的方法，因为每次会得到不一样的结果

<p> Reducer 是纯函数，就可以保证同样的State，必定得到同样的 View。但也正因为这一点，Reducer 函数里面不能改变 State，必须返回一个全新的对象</p>

```js
// State 是一个对象
function reducer(state, action) {
  return Object.assign({}, state, { thingToChange });
  // 或者
  return { ...state, ...newState };
}

// State 是一个数组
function reducer(state, action) {
  return [...state, newItem];
}
```

#### store.subscribe()

<p>Store 允许使用store.subscribe方法设置监听函数，一旦 State 发生变化，就自动执行这个函数。</p>

<p>store.subscribe方法返回一个函数，调用这个函数就可以解除监听。</p>

```js
import { createStore } from 'redux';
const store = createStore(reducer);

store.subscribe(listener);

let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
);
unsubscribe();
```

### createStore的简单实现

```js
const createStore = (reducer) => {
  let state;
  let listeners = [];

  const getState = () => state;

  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
  };

  const subscribe = (listener) => {
    listeners.push(listener);
    return () => {
      listeners = listeners.filter(l => l !== listener);
    }
  };

  dispatch({});

  return { getState, dispatch, subscribe };
};
```

#### Reducer的拆分

<p>Redux 提供了一个combineReducers方法，用于 Reducer 的拆分。你只要定义各个子 Reducer 函数，然后用这个方法，将它们合成一个大的 Reducer。</p>

```js
import { combineReducers } from 'redux';

// state属性与子reducer同名写法
const chatReducer = combineReducers({
  chatLog,
  statusMessage,
  userName
})

// state属性不与子reducer同名写法
const reducer = combineReducers({
  a: doSomethingWithA,
  b: processB,
  c: c
})
```

### combineReducer的简单实现

```js
const combineReducers = reducers => {
  return (state = {}, action) => {
    return Object.keys(reducers).reduce(
      (nextState, key) => {
        nextState[key] = reducers[key](state[key], action);
        return nextState;
      },
      {} 
    );
  };
};
```

#### React中使用redux

<p>React-Redux是一个第三方的模块，可以在 react 中非常方便是使用 redux</p>

```js
ReactDOM.render(
  <Provider store={store}>
    <MyRootComponent />
  </Provider>,
  rootEl
)
```

<p>Provider 实质是一个组件,是一个提供器，是 react-redux 的一个核心API，连接着 store , Provider 里边所有的组件，都有能力获取到 store 里边的内容</p>

<p>根组件应该嵌套在 Provider 中才能使用 connect() 方法</p>

<b>connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])</b>

<p>connect 连接 React 组件与 Redux store。
连接操作不会改变原来的组件类,反而返回一个新的已与 Redux store 连接的组件类。</p>

```js
import React, { Component } from 'react'
import { connect } from 'react-redux'

class TodoList extends Component {
  render () {
    return (
      <div>
        <div>
          <input value={this.props.inputValue} onChange={this.props.handleInputChange} />
          <button>提交</button>
        </div>
        <ul>
          <li>Dell</li>
        </ul>
      </div>
    )
  }
}

//connect 方法的意思是做连接，在做连接时需要有一定的方式和规则,就是用 mapStateToProps 方法来做关联，就是把 store 里的数据 inputValue 映射到组件 inputValue 这个位置，为组件的 props 的数据
const mapStateToProps = (state) => {
  return {
    inputValue: state.inputValue
  }
}

//通过 mapDispatchToProps 方法把 store.dispatch 挂载到props上;dispatch 方法被映射到了 props 上，所以就可以通过 this.props.dispatch 方法去调用了
const mapDispatchToProps = (dispatch) => {
  return {
    handleInputChange(e) {
      const action = {
        type: 'change_input_value',
        value: e.target.value
      }
      dispatch(action)
    }
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(TodoList)
```

```js
// reducer.js
export default (state = defaultState, action) => {
  if (action.type === 'change_input_value') {
    const newState = JSON.parse(JSON.stringify(state))
    newState.inputValue = action.value
    return newState
  }
  return state
}
```