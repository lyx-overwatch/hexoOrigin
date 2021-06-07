---
title: JS eventLoop
date: 2020-09-22 15:56:24
categories:
- js
---
```js
    // 小白入门版
    setTimeout(()=>{
        console.log(1)
    },0)
    Promise.resolve().then(()=>{
        console.log(2)
    })
    console.log(3) //3,2,1
```
```js
    // 游刃有余版
    setTimeout(()=>{
        console.log(1)
    },0)
    let a=new Promise((resolve,reject)=>{
        console.log(2)
        reject('x')
        console.log('resolved') //resolve 或 ，reject之后仍会执行
    }).then(()=>{
        console.log(3)
    },()=>{
        console.log('error1')
        reject('x')
    }).then(()=>{
        console.log(4)
    },()=>{
        console.log('error2')
    }).then(()=>{
        console.log(5)
    }).then(()=>{
        console.log(6)
    })
    console.log(7) // 2,resolved,7,error1,error2,5,6,1
```
<!--more-->
```js
    // 炉火纯青版
    new Promise((resolve,reject)=>{
        console.log("promise1")
        resolve()
    }).then(()=>{
        console.log("then11")
        new Promise((resolve,reject)=>{
            console.log("promise2")
            resolve()
        }).then(()=>{
            console.log("then21")
        }).then(()=>{
            console.log("then22")
        })
    }).then(()=>{
        console.log("then12")
    }) // promise1, then11, promise2, then21, then12, then22

```
```js
    // 登峰造极版
    async function async1() {
        console.log( 'async1 start' )
        await async2() // await async2()相当于一个Promise
        console.log( 'async1 end' )
    }

    async function async2() {
        console.log( 'async2' )
    }

    console.log( 'script start' )

    setTimeout( function () {
        console.log( 'setTimeout' )
    }, 0 )
    
    async1(); // async/await仅仅影响的是函数内的执行，而不会影响到函数体外的执行顺序。
              // 也就是说async1()并不会阻塞后续程序的执行

    new Promise( function ( resolve ) { // 传入Promise()的参数叫做executor,是同步任务
        console.log( 'promise1' )
        resolve();
    } ).then( function () {
        console.log( 'promise2' )
    } )

    console.log( 'script end' )
    // script start , async1 start, async2, promise1, script end ,async1 end, promise2, setTimeout
```