---
title: vue中实现图片的懒加载
categories:
- vue
---
#### 懒加载的结构
```js
<div class="wrapper">
          <img data-src="http://mp-piao-admincp.qunarzz.com/mp_piao_admin_mp_piao_admin/admin/20193/87a224d0349d94a11e97f31aa1aba4f5.jpg_750x200_1f78af87.jpg"
               src=""
               ref="img_one">
          <img data-src="https://mp-piao-admincp.qunarzz.com/mp_piao_admin_mp_piao_admin/admin/20198/06e65ace8dce3638e847b04e975489d1.jpg_750x200_a2dcb0a4.jpg"
               src=""
               ref="img_two">
          <img data-src="https://mp-piao-admincp.qunarzz.com/mp_piao_admin_mp_piao_admin/admin/20197/0b0622e5b6ac5fbed3084dd2832e6f8f.jpg_750x200_895321b6.jpg"
               src=""
               ref="img_three">
          <img data-src="https://mp-piao-admincp.qunarzz.com/mp_piao_admin_mp_piao_admin/admin/20193/2df3ba0f23801d7ed5607f4767a18482.jpg_750x200_b4d6aa93.jpg"
               src=""
               ref="img_four">
    </div>
```
<!-- more -->
不给图片指定src，设置一个data-src的属性，当图片出现在我们的视野中时，将data-src的值赋给src。
#### 判断函数是否在视口内
```js
    setImgSrc: function () { // 实现图片的懒加载
      let obj = this.$refs
      for (let key in obj) {
        if (this.isInSight(obj[key])) {
          obj[key].src = obj[key].dataset.src
        }
      }
    }
    getHeight: function (e) {
      if (!e) { return }
      let top = e.getBoundingClientRect().top // 获取元素相对视口的高度
      return top
    }
    isInSight: function (e) {
      const clientHeight = document.documentElement.clientHeight // 窗口高度
      return this.getHeight(e) < clientHeight
    }
```
#### 使用函数节流防止过多的滚动事件
```js
throttle: function (func, delay) { // 函数节流
      let timeout
      return function () {
        let context = this
        let args = arguments
        if (!timeout) {
          timeout = setTimeout(() => {
            timeout = null
            func.apply(context, args)
          }, delay)
        }
      }
    }
```
最后在created生命周期函数里面设置函数：
```js
created () {
    window.addEventListener('scroll', this.throttle(this.setImgSrc, 100), false)
  }
```
