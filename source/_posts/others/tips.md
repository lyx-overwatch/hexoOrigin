---
title: hexo 使用小技巧
categories:
- others
---
#### 插入图片
<P>1.使用base64图片转换工具,将图片转换为base64格式，使用方法：</P>

```
![alt](data:image/jpeg;base64,/9...)
```
使用相对路径和绝对路径的方法，在hexo上传的时候可能无法显示。

<!-- more -->
<P>2.使用标签插入的方法：</P>
2.1安装修改后兼容hexo3.0版本的hexo-asset-image插件

```
npm install https://github.com/7ym0n/hexo-asset-image --save
```

2.2_config.yml下的url设为网页地址，post_asset_folder设为true，使得hexo n新建文档时会建立一个与文档同名的文件夹，图片资源放在该文件夹内

```
url: http://lyx-overwatch.github.io
post_asset_folder: true
```
2.3标签语法引入

```
{% asset_img 1.png %}
```
这种方式vs插件预览看不到图片，上传到服务器可以看到
#### 文章折叠
在需要折叠的内容前加上这样一行代码：
```js
<!-- more -->
```
