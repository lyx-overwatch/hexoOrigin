---
title: hash路由的简单实现
categories:
- js
---
#### router初始化
```js
function Router() {
        this.routes = {};
        this.currentUrl = '';
    }
Router.prototype.route = function(path, callback) {
    this.routes[path] = callback || function(){};//给不同的hash设置不同的回调函数
};
Router.prototype.refresh = function() {
    this.currentUrl = location.hash.slice(1) || '/';//如果存在hash值则获取到，否则设置hash值为/
    this.routes[this.currentUrl]();//根据当前的hash值来调用相对应的回调函数
};
Router.prototype.init = function() {
    window.addEventListener('load', this.refresh.bind(this), false);
    window.addEventListener('hashchange', this.refresh.bind(this), false);
}
//给window对象挂载属性
window.Router = new Router();
window.Router.init();
```
<!-- more -->
#### 简单定义一个回调函数
```js
var content = document.querySelector('body');
function changeBgColor(color) {
        content.style.backgroundColor = color;
    }
```
#### 不同的hash值执行相应的回调函数
```js
Router.route('/', function() {
        changeBgColor('white');
    });
    Router.route('/blue', function() {
        changeBgColor('blue');
    });
    Router.route('/green', function() {
        changeBgColor('green');
    });
```
#### html结构
```js
<ul>
    <li><a href="#/">turn white</a></li>
    <li><a href="#/blue">turn blue</a></li>
    <li><a href="#/green">turn green</a></li>
</ul>
```
