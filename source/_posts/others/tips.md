---
title: hexo 使用小技巧
categories:
- others
---
#### 插入图片
最稳妥的办法就是使用base64图片转换工具,将图片转换为base64格式，使用方法：
```
![alt](data:image/jpeg;base64,/9...)
```
使用相对路径和绝对路径的方法，在hexo上传的时候无法显示。
<!-- more -->
#### 文章折叠
在需要折叠的内容前加上这样一行代码：
```js
<!-- more -->
```
