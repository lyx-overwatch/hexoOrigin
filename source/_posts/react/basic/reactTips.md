---
title: React 开发技巧
date: 2020-11-05 14:21:48
categories:
- react
---
#### 使用react-loadable实现代码分割，按需加载

<p>react-loadable用法:将要加载的组件和当你加载组件时的“Loading”组件传入一个方法</p>

```js
// 全局方法 lazyRouter.js
import Loadable from 'react-loadable';
import Loading from '@/components/Loading/Loading';

//loader 就是需要加载的组件,Loading 代表正在加载的组件
export default function lazyRouter(loader) {
  return Loadable({
    loader,
    loading: Loading,
  })
}
```

```js
// 在路由调用的地方这样使用
import lazyRouter from "@/common/lazyRouter";

const Home = lazyRouter(() => import("@/routes/Home/Home"));

<Route path={xxx} component={Home} exact />
```

```js
// Loading组件的写法
import React from 'react';
import { Spin, Icon } from 'antd';
import './Loading.scss';

const antIcon = <Icon type="loading" style={{ fontSize: 24 }} spin />;

const Loading = () => (
  <div className="loading-container">
    <Spin indicator={antIcon} className="loading-spin" />
  </div>
);
export default Loading;
```