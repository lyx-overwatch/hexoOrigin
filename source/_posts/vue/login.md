---
title: vue实现拼图验证码登录
categories:
- vue
---
#### canvas绘制背景图
使用Element UI的el-popover组件作为弹出框,组件基本样式如下：
```js
 <el-popover
   popper-class="my-check"
   ref="popover"
   trigger="manual"
   v-model="check"
 >
   <div class="check-img">
     <div class="v-img">
       <canvas id="creatImg"></canvas>
       <canvas id="sliderBlock"></canvas>
     </div>
     <div class="slider">
       <div class="track" id="track">
         {{ tips }}
       </div>
       <div class="button el-icon-s-grid" @touchstart.prevent="drag"></div>
     </div>
     <div class="operation">
       <span
                 title="关闭验证码"
                 @click="change"
                 class="el-icon-circle-close el-icon"
       >
       </span>
       <span
         title="刷新验证码"
         @click="initImg"
         class="el-icon-refresh-left el-icon"
       >
       </span>
     </div>
   </div>
 </el-popover>
```
<!-- more -->
check-img为整个弹出的内容，使用两个canvas绘制图片，第一个用于绘制背景图：
```js
 drawImg () {
      let canvas = document.getElementById('creatImg')
      let context = canvas.getContext('2d')
      let width = canvas.width
      let height = canvas.height
      let slider = document.getElementById('sliderBlock')
      let scontext = slider.getContext('2d')
      // 每次刷新都需要重新绘制
      canvas.height = height
      slider.height = height
      let imgSrc = require('../assets/img/bg.jpg') // 这里不能直接赋值src，需要require
      let img = new Image()
      img.style.objectFit = 'cover'
      img.src = imgSrc
      img.onload = function () {
        context.drawImage(img, 0, 0, width, height)
        scontext.drawImage(img, 0, 0, width, height)
      } // 到这里已经将背景图绘制完成
      let random = Math.random()
        let param1 = {
          'x': 10 + 20 * random,
          'y': 50,
          'w': 40,
          'r': 9
        }
        let param2 = {
          'x': 210 + 20 * random,
          'y': 50,
          'w': 40,
          'r': 9
        }
        this.drawBlock(context, param2, 'fill') // 填充滑块
        this.drawBlock(scontext, param1, 'clip') // 裁剪滑块
 }
```
这里需要用require来获取图片的src，不可以直接赋值一个字符串作为src
#### canvas绘制滑块图片
```js
drawBlock: function (cxt, param, type) { // 绘制滑块
      let x = param.x
      let y = param.y
      let w = param.w
      let r = param.r
      let pi = Math.PI
      cxt.beginPath()
      cxt.moveTo(x, y)
      cxt.arc(x + (w + 5) / 2, y, r, -pi, 0, true)
      cxt.lineTo(x + w + 5, y)
      cxt.arc(x + w + 5, y + w / 2, r, 1.5 * pi, 0.5 * pi, false)
      cxt.lineTo(x + w + 5, y + w)
      cxt.arc(x + (w + 5) / 2, y + w, r, 0, pi, false)
      cxt.lineTo(x, y + w)
      cxt.arc(x, y + w / 2, r, 0.5 * pi, 1.5 * pi, true)
      cxt.lineTo(x, y)
      cxt.lineWidth = 1
      cxt.fillStyle = 'rgba(255, 255, 255, 0.5)'
      cxt.strokeStyle = 'rgba(255, 255, 255, 0.5)'
      cxt.stroke()
      cxt[type]()
      cxt.globalCompositeOperation = 'xor'
      cxt.closePath()
    }
```
param是一个对象，包含了需要裁减或者填充的位置信息
#### 滑块跟随touch事件移动
```js
 drag: function (e) {
      let vue = this // 获取vue对象
      let ele = e.target
      ele.ontouchstart = function (event) {
        let that = this // 获取当前dom对象
        let x = event.changedTouches[0].clientX
        let slider = document.getElementById('sliderBlock')
        const X = 200
        let inside = false
        let insideX = 10 // 定义一个距离判断是否重合
        let movex = 0 // 定义相对起始位置移动的距离
        let move = function (e) {
          movex = e.changedTouches[0].clientX - x // 光标移动距离
          that.style.left = movex > 0 && movex <= 220 ? movex + 'px' : ''
          slider.style.left = movex > 0 && movex <= 220 ? movex + 'px' : ''
        }
        let update = function () {
          document.removeEventListener('touchmove', move)
          document.removeEventListener('touchend', update)
          insideX = X - movex
          if (insideX >= 0 && insideX <= 7) {
            inside = true
          }
          if (!inside) {
            that.style.left = ''
            slider.style.left = ''
            vue.tips = '验证失败'
            setTimeout(() => {
              vue.tips = '请滑动左边滑块完成拼图'
            }, 1000)
          } else {
            vue.tips = '验证成功'
            vue.succ = true
          }
        }
        document.addEventListener('touchmove', move)
        document.addEventListener('touchend', update)
      }
    }
```
e.changedTouches保存的是一个touchList对象集合，第一个对象的clientX就是手指在屏幕上移动时所在的位置
