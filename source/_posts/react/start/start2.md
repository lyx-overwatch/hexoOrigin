---
title: React Hook基础
date: 2020-09-25 15:24:19
categories:
- react
---
#### React Hook 是什么

<p>Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。</p>

<p>Hook 不能在 class 组件中使用 —— 这使得你不使用 class 也能使用 React。React 内置了一些像 useState 这样的 Hook。你也可以创建你自己的 Hook 来复用不同组件之间的状态逻辑。</p>

#### Hook 使用规则

<p>Hook 就是 JavaScript 函数，但是使用它们会有两个额外的规则：</p>

1. 只能在函数最外层调用 Hook。不要在循环、条件判断或者子函数中调用。
2. 只能在 React 的函数组件或自定义Hook中调用 Hook。不要在其他 JavaScript 函数中调用。

## 使用 State Hook

<!--more-->

实现一个计数器，class写法示例：
```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

##### 使用 State Hook的方法实现计数器
```js
// 引入React中useState的Hook 
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

#### 调用 useState 方法的时候做了什么?

<p>它定义一个 “state 变量”。我们的变量叫 count， 但是我们可以叫他任何名字，比如 banana。这是一种在函数调用时保存变量的方式 —— useState 是一种新方法，它与 class 里面的 this.state 提供的功能完全相同。一般来说，在函数退出后变量就会”消失”，而 state 中的变量会被 React 保留。</p>

#### useState 需要哪些参数？

 <p>useState() 方法里面唯一的参数就是初始 state。不同于 class 的是，我们可以按照需要使用数字或字符串对其进行赋值，而不一定是对象。在示例中，只需使用数字来记录用户点击次数，所以我们传了 0 作为变量的初始 state。（如果我们想要在 state 中存储两个不同的变量，只需调用 useState() 两次即可。）</p>

#### useState方法的返回值是什么？

<p>返回值为：当前 state 以及更新 state 的函数。这就是我们写 const [count, setCount] = useState() 的原因。这与 class 里面 this.state.count 和 this.setState 类似，唯一区别就是你需要成对的获取它们。</p>

下面来看下const [count, setCount] = useState(0)的意义：
<p><b>我们声明了一个叫 count 的 state 变量，然后把它设为 0。React 会在重复渲染时记住它当前的值，并且提供最新的值给我们的函数。我们可以通过调用 setCount 来更新当前的 count。</b></p>

#### 读取 State

```
// class 写法
<p>You clicked {this.state.count} times</p>
// hook 写法
<p>You clicked {count} times</p>
```

#### 更新 State

```
// class 写法
<button onClick={() => this.setState({ count: this.state.count + 1 })}>
    Click me
</button>

// hook 写法
<button onClick={() => setCount(count + 1)}>
    Click me
</button>
```

## 使用 Effect Hook

<p>Effect Hook 可以让你在函数组件中执行副作用操作</p>

```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

<p><b>useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。</b></p>

<p>在 React class 中，我们把副作用操作放到 componentDidMount 和 componentDidUpdate 函数中。</p>

<p>这是一个 React 计数器的 class 组件。它在 React 对 DOM 进行操作之后，立即更新了 document 的 title 属性</p>

```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

##### 使用hook的示例
```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

<p><b>useEffect 做了什么？</b></p>
<p>告诉 React 组件需要在渲染后执行某些操作。React 会保存你传递的函数（我们将它称之为 “effect”），并且在执行 DOM 更新之后调用它。</p>

<p><b>为什么在组件内部调用 useEffect？</b></p>
<p>将 useEffect 放在组件内部让我们可以在 effect 中直接访问 count state 变量（或其他 props）。Hook 使用了 JavaScript 的闭包机制</p>

<p><b>useEffect 会在每次渲染后都执行吗？</b></p>
<p>默认情况下，它在第一次渲染之后和每次更新之后都会执行。</p>

#### 需要清除的Effect

<p>某些副作用的清除工作是非常重要的，可以防止引起内存泄露，以下来对比class实现和hook实现</p>

##### class实现

```js
//  在componentDidMount中设置，并在 componentWillUnmount 中清除它
class FriendStatus extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }

  render() {
    if (this.state.isOnline === null) {
      return 'Loading...';
    }
    return this.state.isOnline ? 'Online' : 'Offline';
  }
}
```
##### hook实现

```js
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Specify how to clean up after this effect:
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

<p><b>为什么要在 effect 中返回一个函数？</b></p>
<p>这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。</p>

<p><b>React 何时清除 effect？</b></p>
<p>React 会在组件卸载的时候执行清除操作。正如之前学到的，effect 在每次渲染的时候都会执行。这就是为什么 React 会在执行当前 effect 之前对上一个 effect 进行清除。</p>

## 自定义 Hook

<b>自定义 Hook 是一个函数，其名称以 “use” 开头，函数内部可以调用其他的 Hook。</b>

```js
import { useState, useEffect } from 'react';

//自定义hook useFriendStatus
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

<p><b>自定义 Hook 不需要具有特殊的标识。我们可以自由的决定它的参数是什么，以及它应该返回什么。</b></p>

<p><b>它就像一个正常的函数。但是它的名字应该始终以 use 开头</b></p>

##### 使用自定义hook useFriendStatus

```js
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

<p><b>在两个组件中使用相同的 Hook 会共享 state 吗？</b><p>
<p>不会。自定义 Hook 是一种重用状态逻辑的机制(例如设置为订阅并存储当前值)，所以每次使用自定义 Hook 时，其中的所有 state 和副作用都是完全隔离的</p>

<p><b>自定义 Hook 如何获取独立的 state？</b><p>
<p>每次调用 Hook，它都会获取独立的 state。由于我们直接调用了 useFriendStatus，从 React 的角度来看，我们的组件只是调用了 useState 和 useEffect。 我们可以在一个组件中多次调用 useState 和 useEffect，它们是完全独立的。
</p>