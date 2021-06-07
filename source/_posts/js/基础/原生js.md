---
title: JS基础知识总结
date: 2020-09-22 15:47:43
categories:
- js
---
### 闭包
```js
// 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。
    function per() {
        var name = 'lyx'
        function closure() {
            return name
        }
        return closure
    }
    var f1 = per() // 本来函数per执行完，所有变量都销毁，但是产生了闭包，闭包仍然持有对该作用域的引用
    alert(f1()) // f1执行在closure的词法作用域之外
```
### 函数防抖
<!--more-->

```js
//所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。
    let num = 1;
    let content = document.getElementById('content');

    function count() {
        content.innerHTML = num++;
    };

    function debounce(func,wait){
        let timeout;
        return function () {
            let context=this;
            let args=arguments;

            if(timeout){
                clearTimeout(timeout);
            }
            
            timeout=setTimeout(()=>{
                func.apply(context,args);
            },wait);
        }
    }
    content.onmousemove = debounce(count,1000);
```
### 函数节流
```js
 //所谓节流，就是指连续触发事件但是在 n 秒中只执行一次函数。 节流会稀释函数的执行频率。
    let num = 1;
    let content = document.getElementById('content');

    function count() {
        content.innerHTML = num++;
    };

    function throttle(func,wait){
        let timeout;
        return function () {
            let context=this;
            let args=arguments;

            if(!timeout){
                timeout=setTimeout(()=>{
                    timeout=null;
                    func.apply(context,args);
                },wait);
            }
        }
    }
    content.onmousemove = throttle(count,1000);
```
### 事件捕获机制
```html
<body>
<div id="s1" style="width: 100%;height:40%;background-color: #7FB4C7">s1
    <div id="s2" style="width: 100%;height: 50%;background-color: saddlebrown">s2</div>
</div>
<script>
    //冒泡和捕获同时存在，document 往 target节点，捕获前进，遇到注册的捕获事件立即触发执行；
    //到达target节点，触发事件（对于target节点上，是先捕获还是先冒泡则捕获事件和冒泡事件的注册顺序，先注册先执行）
    //target节点 往 document 方向，冒泡前进，遇到注册的冒泡事件立即触发
    var s1=document.getElementById('s1');
    var s2=document.getElementById('s2');
    s2.addEventListener("click",function(e){
        console.log("s2 冒泡事件");},false);
    s2.addEventListener("click",function(e){
        console.log("s2 捕获事件");},true);
    s1.addEventListener("click",function(e){
        console.log("s1 捕获事件");},true);
    s1.addEventListener("click",function(e){
        console.log("s1 冒泡事件"); },false);
    //s1 捕获事件
    // s2 冒泡事件
    // s2 捕获事件
    // s1 冒泡事件
</script>
</body>
```
### 实现new操作符
```js
    function creatNew(origin,...args) {
        let obj={};
        Object.setPrototypeOf(obj,origin.prototype);
        let res=origin.apply(obj,args);
        return res instanceof Object ? res : obj;
    }
```
### 原生ajax使用方法
```html
<body>
<input type="button" value="请求" id="bt">
<span id="sp"></span>
<script>
    window.onload=function () {
        var obt=document.getElementById('bt');
        var ospan=document.getElementById('sp');
        obt.onclick=function () {
            var xhr;
            if (window.XMLHttpRequest){
                xhr=new XMLHttpRequest();
            }else{
                xhr=new ActiveXObject("Microsoft.XMLHTTP");
            }
            xhr.onreadystatechange=function () {
                if(xhr.status==200&&xhr.readyState==4){
                    var res=JSON.parse(xhr.responseText);
                    ospan.innerText=res.name;
                }
            }
            xhr.open('get','testJson.json',true);
            xhr.send();
        }
    }
</script>
</body>
```