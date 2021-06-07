---
title: ref转发以及useImperativeHandle hook函数的使用
date: 2020-11-25 14:40:25
categories:
- react
---
### ref转发的概念

<p>ref 转发（也叫透传），是使用 React.forwardRef 方法实现的，该方法返回一个组件，参数为函数（props callback），函数的第一个参数为父组件传递的 props，第二给参数为父组件传递的 ref，其目的就是希望可以在封装组件时，外层组件可以通过 ref 直接控制内层组件或元素的行为。</p>

#### 不使用useImperativeHandle进行ref转发的例子

```jsx
import React, { useCallback, useRef } from 'react';

const FancyButton = React.forwardRef((props, pref) => (
  <div>
    <input ref={pref} type='text' />
    <button>{props.children}</button>
  </div>
))

export default () => {
  const ref = useRef()
  const handleClick = useCallback(() => ref.current.focus(),[ref])
  /*
  useCallback的使用场景：
  有一个父组件，其中包含子组件，子组件接收一个函数作为props；通常而言，如果父组件更新了，子组件也会执行更新；但是大多数场景下，更新是没有必要的，我们可以借助useCallback来返回函数，然后把这个函数作为props传递给子组件；这样，子组件就能避免不必要的更新。
  useCallback 返回的是缓存的函数，当依赖的ref发生变化时，就会计算新的函数返回，这样就能减少不必要的渲染.
  */
  return (
    <div>
        <FancyButton ref={ref}>Click Me</FancyButton>
        <button onClick={handleClick}>获取焦点</button>
    </div>
  )
}
```

<p>这个例子实际上是直接转发ref，直接转发 ref 是将 React.forwardRef 中函数上的 ref 参数直接应用在了返回元素的 ref 属性上，其实父、子组件引用的是同一个 ref 的 current 对象，官方并不推荐这样</p>

#### 使用useImperativeHandle进行ref转发

```jsx
import React, { useImperativeHandle, useRef } from 'react';

const FancyInput = React.forwardRef((props, pref) => {
  const ref = useRef()
  // 第一个参数是目标元素的ref引用
  useImperativeHandle(pref, ()=> ({
    // 暴露给父组件的方法
    focus: () => {
      ref.current.focus()
    }
  }))
  return (
      <input ref={ref} type="text"/>
  )
})

export default () => {
  const fancyInputRef = useRef()

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button onClick={() => fancyInputRef.current.focus()}>
        父组件调用子组件的 focus
      </button>
    </div>
  )
}
```

<p>使用 useImperativeHandle 后，可以让父、子组件分别有自己的 ref，通过 React.forwardRef 将父组件的 ref 透传过来，通过 useImperativeHandle 方法来自定义开放给父组件的 current。<b>useImperativeHandle必须和React.forwardRef配合使用</b></p>