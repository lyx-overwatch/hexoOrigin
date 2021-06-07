---
title: CSS margin
date: 2020-09-26 15:16:02
categories:
- others
---
#### 百分比margin

<p>普通元素百分比margin都是相对于容器宽度计算的</p>

<p>绝对定位元素的百分比margin是相对第一个定位祖先元素（relative、absolute，fixed）的宽度计算的</p>

#### margin重叠

<p>margin重叠的3种情境</p>

1. 相邻的兄弟元素
2. 父级和第一个/最后一个子元素
3. 空的block元素

<p>margin重叠通常特性</p>

1. block水平元素，不包括float和absolute元素
2. 不考虑writing-mode，只发生在垂直方向margin-top，margin-bottom

<!--more-->
<p>margin重叠的计算规则</p>

1. 正正取最大
2. 正负值相加
3. 负负最负值

<p>破坏margin重叠</p>

1. 父级或子元素使用浮动或者绝对定位absolute，浮动或绝对定位不参与margin的折叠
2. 父级overflow:hidden;
3. 父级设置padding（破坏非空白的折叠条件）
4. 父级设置border （破坏非空白的折叠条件）

#### margin: 0 auto实现块元素水平居中

<p>margin: 0 auto 实际相当于 margin: 0 auto 0 auto,水平方向的 auto,其计算值取决于可用空间（剩余空间）</p>

<p>图片元素设置margin:0 auto不居中，因为图片是inline水平，就算没有width，它也不会占据整个容器，设置图片display:block就可以居中了</p>

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .img-content {
      width: 800px;
      height: 400px;
      background-color: green;
    }
    .img {
      display: block;
      width: 600px;
      height: 400px;
      margin: 0 auto
    }
  </style>
</head>
<body>
  <div class='img-content'>
    <img class='img' src='./img/Genji03.jpg'>
  </div>
</nav>
</body>
</html>
```

<p>最终效果</p>

{% asset_img 1.png %}