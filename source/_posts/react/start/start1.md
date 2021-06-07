---
title: react基础
date: 2020-09-22 11:56:29
categories:
- react
---
#### 初识react

<p>使用create-react-app创建一个简单的应用:</p>

{% asset_img 1.png %}

<p>这是一个简单的井子棋游戏，每个棋格都是一个按钮，将一个棋格作为一个最简单的组件进行封装。</p>

<!-- more -->
```js
import React from 'react'
import '../css/square.css'

// class Square extends React.Component {
//   render () {
//     return (
//       <button className="square"
//        onClick={() => {this.props.onClick()}}>
//         {this.props.value}
//       </button>
//     );
//   }
// }

/* 
  函数组件写法
*/
function Square(props) {
    return (
      <button className="square"
       onClick={() => {props.onClick()}}>
        {props.value}
      </button>
    )
}
export default  Square
```
<p>如果组件不包含state，并且只要一个render方法时，使用函数式组件比较简单。这个函数接收 props 作为参数，然后返回需要渲染的元素。
棋盘由9个棋格组成，state中包含了一个棋盘数组，保存所有的棋格的内容。这样每个的棋格数据改变的时候，不用为每个棋格添加state保存数据，只需在所有棋格的父组件——棋盘中的state来维护和传递数据（这里可称为状态提升——多个组件需要反映相同的变化数据，这时我们建议将共享状态提升到最近的共同父组件中去）。
这里注意class组件的写法，在constructor内使用super（props）调用父类的构造函数。</p>

```js
import React from 'react'
import Square from './square'
import '../css/border.css'
import calculateWinner from './calculateWinner'

class Border extends React.Component {
  constructor (props) {
    super(props)
    this.state = {
      squares: Array(9).fill(null),
      xIsNext: true
    }
  }

  renderSquare (i) {
    return (
      <Square value={this.state.squares[i]}
      onClick={() => {this.handleClick(i)}} />
    )
  }

  handleClick(i) {
    const squares = this.state.squares.slice()
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    squares[i] = this.state.xIsNext ? 'X' : 'O'
    this.setState({squares,xIsNext:!this.state.xIsNext})
  }

  render () {
    let winner = calculateWinner(this.state.squares)
    let status
    if (winner) {
      status = 'Winner Is : ' + winner
    } else {
      status = 'Next Player : ' + (this.state.xIsNext ? 'X' : 'O') 
    }
    return (
      <div>
        <div>{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    )
  }
}

export default Border
```
<P>改变state的数据，使用this.setState方法；this.state.xxx只能从state中读取数据。</P>
<P>JSX是一个 JavaScript 的语法扩展。 React 中配合使用 JSX，JSX 可以很好地描述 UI 应该呈现出它应有交互的本质形式</P>

{% asset_img 2.png %}

#### React 列表 & key的使用

```js
function ListItem(props) {
  // 正确！这里不需要指定 key：
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 正确！key 应该在数组的上下文中被指定
    <ListItem key={number.toString()} value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

#### 受控组件

<p>在React中，所谓受控组件和非受控组件，是针对表单而言的</p>

1. 表单元素依赖于状态，表单元素需要默认值实时映射到状态的时候，就是受控组件，这个和双向绑定相似.
2. 受控组件，表单元素的修改会实时映射到状态值上，此时就可以对输入的内容进行校验.
3. 受控组件只有继承React.Component才会有状态.
4. 受控组件必须要在表单上使用onChange事件来绑定对应的事件.

#### 非受控组件

1. 非受控组件即不受状态的控制，获取数据就是相当于操作DOM。
2. 非受控组件的好处是很容易和第三方组件结合。

#### 状态提升

<b>在 React 中，将多个组件中需要共享的 state 向上移动到它们的最近共同父组件中，便可实现共享 state。这就是所谓的“状态提升”。</b>


```js
// 父组件
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
    this.state = {temperature: '', scale: 'c'};
  }

  handleCelsiusChange(temperature) {
    this.setState({scale: 'c', temperature});
  }

  handleFahrenheitChange(temperature) {
    this.setState({scale: 'f', temperature});
  }

  render() {
    const scale = this.state.scale;
    const temperature = this.state.temperature;
    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;

    return (
      <div>
        <TemperatureInput
          scale="c"
          temperature={celsius}
          onTemperatureChange={this.handleCelsiusChange} />
        <TemperatureInput
          scale="f"
          temperature={fahrenheit}
          onTemperatureChange={this.handleFahrenheitChange} />
        <BoilingVerdict
          celsius={parseFloat(celsius)} />
      </div>
    );
  }
}
```

```js
// 子组件
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

// 子组件接收父组件props传来的方法改变父组件state里面的值
  handleChange(e) {
    this.props.onTemperatureChange(e.target.value);
  }

  render() {
    const temperature = this.props.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```