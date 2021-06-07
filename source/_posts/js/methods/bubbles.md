---
title: 绘制气泡
categories:
- js
---
#### 使用canvas绘制气泡效果

完整代码：

<!-- more -->

```js
function () {
    con.innerHTML= '<canvas id="canvas" style="margin: 0px;display: block;background:linear-gradient(180deg, #527785 0%, #7FB4C7 100%);width: 100%;height:100%"></canvas>';
    // con是包含canvas的一个容器
    let content=document.getElementById('canvas');
    let WIDTH=content.clientWidth;
    let HEIGHT=content.clientHeight;

    let bubbles=[]; // 初始化气泡数组

    var canvas=document.getElementById('canvas');
    canvas.width=WIDTH;
    canvas.height=HEIGHT;

    if(canvas.getContext('2d')){
        const context = canvas.getContext('2d');
        initBubbles(context);//初始化气泡

        setInterval(function () {
            renderBubbles(context);//绘制气泡
            updateBubbles();//更新气泡位置
        },50);

    }else {
        alert('当前浏览器不支持canvas');
    }

    function initBubbles(cxt) {
        cxt.clearRect(0,0,WIDTH,HEIGHT); // 清屏
        let miniDanWid=WIDTH/20;
        for(let x=1; x<20;x++){
            let radius=5*(1+Math.random()); // 随机给气泡设置半径
            let Height=HEIGHT-radius;

            cxt.strokeStyle='white';
            cxt.beginPath();
            cxt.arc(x*miniDanWid,Height,radius,0,2*Math.PI,true); // 画圆
            cxt.closePath();
            cxt.stroke(); // 描边

            let bubble={
                x:x*miniDanWid,
                y:Height,
                v:2*(1+Math.random()),
                r:radius
            }
            bubbles.push(bubble);
        }
    }

    function renderBubbles(cxt) { // 气泡渲染
        cxt.clearRect(0,0,WIDTH,HEIGHT);
        for(let i=0;i<bubbles.length;i++){
            cxt.strokeStyle='white';
            cxt.beginPath();
            cxt.arc(bubbles[i].x,bubbles[i].y,bubbles[i].r,0,2*Math.PI,true);
            cxt.closePath();
            cxt.stroke();
        }
    }

    function updateBubbles() { // 更新气泡位置
        for(let i=0;i<bubbles.length;i++){
            bubbles[i].y-=bubbles[i].v;
            if(bubbles[i].y<=bubbles[i].r){
                bubbles[i].y=HEIGHT-bubbles[i].r;
            }
        }
    }

}
```

最后气泡效果图：

{% asset_img 1.png %}