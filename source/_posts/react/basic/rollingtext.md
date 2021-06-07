---
title: react开发文字滚动跑马灯
date: 2021-03-29 14:56:42
categories:
- react
---
### 文字跑马灯的实现原理

<p>前提条件，假设文字很长很长；文字的容器A，A的父容器B，B的长度小于A的长度，给B容器设置overflow: hidden，强制不换行属性white-space: nowrap;</p>

<p>实现思路就是将A容器从左向右滚动，滚动的距离就是A的长度 - B的长度
(如果是移动端，<b>1px的css像素(独立像素)并不等于1px的屏幕物理像素</b>；项目使用rem布局，200px宽度的设计稿长度在屏幕上css只有100px宽度，可以理解为<b>1px的css独立像素等于2px的屏幕物理像素</b>；所以在设置css3动画的时候，项目中的滚动距离就是<b>(A的长度 - B的长度) / 2</b>了)，下面是js动画和css3动画两种实现方法的具体代码</p>

### 使用window.requestAnimationFrame动画

<!--more--->

<b>RollingText.tsx</b>

```jsx
import React, { useImperativeHandle, useRef, useState } from 'react'
import {useMount} from 'ahooks'
import './index.css'

export interface RollingTextProps extends React.HTMLAttributes<HTMLDivElement> {
  rolling: boolean
  rollingBack: boolean
  interval: number
  speedTimes: number
  text: string
}

const RollingText: React.FC<RollingTextProps> = React.forwardRef(
  (props: any, pRef: any) => {
    const {
      rolling = true,
      rollingBack = true,
      interval = 60,
      speedTimes = 1,
      text,
      ...otherProps
    } = props
    const ref = useRef<HTMLDivElement>(null)
    useImperativeHandle(pRef, () => ref.current)

    const [offset, setOffset] = useState(0)

    useMount(() => {
      start(offset)
    })

    const start =(left: number) => {
      if (!rolling) return 
      if (ref && ref.current) {
        // 容器宽度
        let wrapperWidth = ref.current.clientWidth
        let span = ref.current.childNodes[0]
        // 内容宽度
       const { offsetWidth }  = span
       const leftEndDistance = offsetWidth - wrapperWidth

        const timer = window.requestAnimationFrame(() => {
          if (-left <= leftEndDistance) {
            const speed = interval / 60 *speedTimes * 1
            start(left - speed)
            setOffset(left - speed)
          } else {
            if (rollingBack) {
              rollingBackAnimation(left)
            } else {
              window.cancelAnimationFrame(timer)
              start(0)
              setOffset(0)
            }
          }
        })
      }
    }

    const rollingBackAnimation = (right: number) => {
      const timer = window.requestAnimationFrame(() => {
        if (right <= 0) {
          const speed = (interval / 60) * speedTimes * 1
          rollingBackAnimation(right + speed)
          setOffset(right + speed)
        } else {
          window.cancelAnimationFrame(timer)
          start(0)
        }
      })
    }
    
    return (
      <div
        ref={ref}
        className={'rolling-text-contain'}
        {...otherProps}
      >
        <span
          className={'rolling-text'}
          style={{transform: `translate3d(${offset}px,0,0)`}}
        >
          {text}
        </span>
      </div>
    )
  }
)

export default RollingText
```

<b>RollingText.css</b>

```css
.rolling-text-contain {
  position: relative;
  overflow: hidden;
  white-space: nowrap;
}

.rolling-text {
  display: inline-block;
  height: 100%;
}
```

<p>写完了在pc端看着很流畅，移动端项目使用文字滚动的也很流畅，感觉如流水一般；本以为万事大吉，但是问题来了；
有个大佬说苹果手机打开项目首页不一会就发热特别严重，我通过排查发现是这个组件的问题，好家伙能把手机弄到发热，屏幕滑动的时候整个界面都卡，这咋搞？</p>

### 创建css3动画，通过js动态创建css3 @keyframes规则

<b>下面是经过优化的RollingText.tsx</b>

```jsx
import React, { useImperativeHandle, useRef, useState, useEffect } from 'react';
import { useMount } from 'ahooks';
import './RollingText.css';

export interface RollingTextProps extends React.HTMLAttributes<HTMLDivElement> {
  array: Array<string>; // 跑马灯数据源
  waitSecond: number; // 文字不够长不滚动时，切换下一条的等待时间
  rolling: boolean // 是否滚动
  speedTimes: number // 滚动速度
  behindfix: string // 添加后缀保证唯一性
}

const relationObj = {};

const RollingText: React.FC<RollingTextProps> = React.forwardRef((props, pRef) => {
  const {
    array,
    waitSecond = 10,
    rolling = true,
    speedTimes = 1,
    behindfix,
    ...otherProps
  } = props;
  const ref = useRef<HTMLDivElement>(null);
  const sRef = useRef<HTMLDivElement>(null);
  useImperativeHandle(pRef, () => ref.current);

  const [curText, setText] = useState(array[0]);

  useMount(() => {
    relationObj[`${behindfix}`] = {};
    relationObj[`${behindfix}`].index = 0;
    relationObj[`${behindfix}`].timer = null;
  });

  useEffect(() => {
    startMove(sRef);
    return () => clearInterval(relationObj[`${behindfix}`].timer);
  }, [curText, array]);

  const startMove = (rollingText: any) => {
    if (!rolling) return;
    if (ref && ref.current && rollingText && rollingText.current && curText) {
      const { clientWidth } = ref.current;
      const span = rollingText.current;
      const { offsetWidth } = span;
      const rollingDistance = offsetWidth - clientWidth;
      const animationName = `rolling${
        behindfix + new Date().getTime().toString()
      }`;
      const animationTime =
        rollingDistance > 0
          ? Math.ceil(rollingDistance / (60 * speedTimes))
          : waitSecond;

      // css3 动画规则
      const rollingTextAnimation = `@keyframes ${animationName} {
            0% {
              transform: translateX(0);
              -webkit-transform: translateX(0);
            }
            100% {
              transform: translateX(-${rollingDistance / 2}px);
              -webkit-transform: translateX(-${rollingDistance / 2}px);
            }
          }`;

      const { length } = document.styleSheets[0].cssRules;
      
      if (!relationObj[`${behindfix}`].cssRulerIndex) {
        relationObj[`${behindfix}`].cssRulerIndex = length;
        document.styleSheets[0].insertRule(
          rollingTextAnimation,
          relationObj[`${behindfix}`].cssRulerIndex
        );
      } else {
        // 添加新规则前，删除原来的规则
        document.styleSheets[0].deleteRule(
          relationObj[`${behindfix}`].cssRulerIndex
        );
        document.styleSheets[0].insertRule(
          rollingTextAnimation,
          relationObj[`${behindfix}`].cssRulerIndex
        );
      }
      span.style.animation = `${animationName} ${
        animationTime === waitSecond ? 0 : animationTime
      }s infinite linear`;

      relationObj[`${behindfix}`].timer = setInterval(() => {
        relationObj[`${behindfix}`].index += 1;
        setText(array[relationObj[`${behindfix}`].index % array.length]);
      }, animationTime * 1000);
    } else {
      relationObj[`${behindfix}`].timer = setInterval(() => {
        setText(array[relationObj[`${behindfix}`].index % array.length]);
      }, 0);
    }
  };

  return (
    <div className="rolling-text-contain" ref={ref} {...otherProps}>
      <span
        className="rolling-text"
        ref={sRef}
      >
        {curText}
      </span>
    </div>
  );
}
);

export default RollingText;
```

<b>RollingText.css</b>

```css
.rolling-text-contain {
  position: relative;
  overflow: hidden;
  white-space: nowrap;
}

.rolling-text {
  display: inline-block;
  height: 100%;
}
```

<p>移动端项目使用css3动画的跑马灯可以节省很多性能消耗，PC端cpu性能充足，使用两种方式都可</p>