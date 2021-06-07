---
title: react类组件构造函数为何需要绑定this
date: 2021-03-29 14:47:32
categories:
- react
---
<p>开发react类组件的时候，我们总是会在构造函数中绑定this，为何需要绑定this，不绑定会发生什么</p>

```jsx
class Button extends React.Component{
  constructor(props) {
    super(props)
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick(e){
    console.log(this)
  }
 
  render(){
    return (
      <button type="button" onClick={this.handleClick}>
        Click Me
      </button>
    )
  }
}
 
ReactDOM.render(
  <Button />,
  document.getElementById("app")
)
```

### REACT类组件绑定THIS，实际与JAVASCRIPT语言特性有关

<!--more--->

```js
class A {
  constructor(name) {
    this.name = name
  }
  showName(){
    console.log(this.name)
  }
}

let a = new A('lily')
a.showName() // lily

/* 下面的赋值模拟了上下文的丢失,
和react component中将处理程序作为回调参数传递类似*/

let func = a.showName
func() // TypeError: this is undefined
```

<p>类声明和类表达式的主体使用严格模式，包括构造函数，静态方法和原型方法；上面代码展示了不绑定this的情况下，this指向undefined，造成上下文丢失的情况。</p>

### 为了避免这种情况，我们需要明确的绑定THIS

```js
class A {
  constructor(name) {
    this.name = name
  }
  showName() {
    console.log(this.name)
  }
}

let a = new A('lily')
a.showName = a.showName.bind(a)
a.showName() // lily

let func = a.showName
func() // lily
```

<p>由于构造函数是类初始化的地方，在里面编写绑定事件再合适不过了</p>

```js
class A {
  constructor(name) {
    this.name = name
    this.showName = this.showName.bind(this)
  }
  showName() {
    console.log(this.name)
  }
}

let a = new A('lily')
a.showName() // lily

let func = a.showName
func() // lily
```

### 使用箭头函数可以不用绑定THIS

```js
class A {
  constructor(name) {
    this.name = name
  }
  showName = () => {
    console.log(this.name)
  }
}

let a1 = new A('lily')
a1.showName() // lily

let func1 = a1.showName
func1() // lily

let a2 = new A('lucy')
a2.showName() // lucy

let func2 = a2.showName
func2() // lucy
```

<p>箭头函数本身没有this，它的函数体里的this获取的是箭头函数定义时所在的上下文;在上述例子中，箭头函数被包含在类中，它的this就是类的实例化对象</p>