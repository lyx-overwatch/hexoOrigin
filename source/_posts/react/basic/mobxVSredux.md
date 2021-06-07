---
title: Mobx VS Redux
date: 2020-11-04 15:42:03
categories:
- react
---
<p>Mobx和Redux都是JavaScript应用状态管理库，都适用于React，Angular，VueJs等框架或库，而不是局限于某一特定UI库。</p>

#### Redux
<p>Redux更多的是遵循Flux模式的一种实现，是一个JavaScript库，它关注点主要是以下几方面</p>

1. Action：一个JavaScript对象，描述动作相关信息，主要包含type属性和payload属性：
    1. type：action 类型；
    2. payload：负载数据；
2. Reducer：定义应用状态如何响应不同动作（action），如何更新状态；
3. Store：管理action和reducer及其关系的对象，主要提供以下功能：
   1. 维护应用状态并支持访问状态（getState()）；
   2. 支持监听action的分发，更新状态（dispatch(action)）；
   3. 支持订阅store的变更（subscribe(listener)）；
4. 异步流：由于Redux所有对store状态的变更，都应该通过action触发，异步任务（通常都是业务或获取数据任务）也不例外，而为了不将业务或数据相关的任务混入React组件中，就需要使用其他框架配合管理异步任务流程，如redux-thunk，redux-saga等

#### Mobx

<p>Mobx是一个透明函数响应式编程:</p>

1. Action：定义改变状态的动作函数，包括如何变更状态；
2. Store：集中管理模块状态（State）和动作（action）；
3. Derivation（衍生）：从应用状态中派生而出，且没有任何其他影响的数据，我们称为derivation（衍生），衍生在以下情况下存在：
    1. 用户界面;
    2. 衍生数据:<p>衍生主要有两种：Computed Values（计算值）：计算值总是可以使用纯函数（pure function）从当前可观察状态中获取；
Reactions（反应）：反应指状态变更时需要自动发生的副作用，这种情况下，我们需要实现其读写操作</p>

<!--more-->

#### 二者对比

<p>Redux更多的是遵循函数式编程思想，而Mobx则更多从面向对象角度考虑问题。Redux提倡编写<b>函数式代码</b>，如reducer就是一个纯函数(pure function);Mobx设计更多偏向于面向对象编程和响应式编程，通常将状态包装成可观察对象，于是我们就可以使用可观察对象的所有能力，一旦状态对象变更，就能自动获得更新。</p>

<p>store是应用管理数据的地方，在Redux应用中，我们总是将所有共享的应用数据集中在一个大的store中，而Mobx则通常按模块将应用状态划分，在多个独立的store中管理。</p>

<p>Redux默认以JavaScript原生对象形式存储数据，而Mobx使用可观察对象.

1. Redux需要手动追踪所有状态对象的变更；
2. Mobx中可以监听可观察对象，当其变更时将自动触发监听；
</p>

<p>Redux状态对象通常是不可变的，我们不能直接操作状态对象，而总是在原来状态对象基础上返回一个新的状态对象，这样就能很方便的返回应用上一状态；而Mobx中可以直接使用新值更新状态对象。</p>

#### mobx-react和react-redux

<p>使用Redux和React应用连接时，需要使用react-redux提供的Provider和connect：

1. Provider：负责将Store注入React应用；
2. connect：负责将store state注入容器组件，并选择特定状态作为容器组件props传递</p>

<p>对于Mobx而言，同样需要两个步骤：

1. Provider：使用mobx-react提供的Provider将所有stores注入应用；
2. 使用<b>inject</b>将特定store注入某组件，store可以传递状态或action；然后使用observer保证组件能响应store中的可观察对象（observable）变更，即store更新，组件视图响应式更新。</p>