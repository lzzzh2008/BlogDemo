---
title: JS 中的event loop
date: 2018-07-24 11:14:17
tags: JavaScript
---
# JS异步事件执行机制 
## 先看案例一
```JavaScript
console.log(1)
setTimeout(function() {
    console.log(2)
}, 0)
console.log(3)
```
*运行结果是：1 3 2*

也就是说，setTimeout里的函数并没有立即执行，而是延迟了一段时间，满足一定条件后才去执行的，这类代码，我们叫异步代码。
<!-- more -->
所以，这里我们首先知道了JS里的一种分类方式，就是将任务分为：同步任务和异步任务。

按照这种分类方式,JS的执行机制是：

>**1. 首先判断JS是同步还是异步，同步就进入主进程，异步就进入event table**  
**2. 异步任务在event table中注册函数，当满足触发条件后，被推入event queue**  
**3. 同步任务进入主线程后一直执行，直到主线程空闲时，才会去event queue中查看是否有可执行的异步任务，如果有就推入主进程中**  

以上三步循环，这就是event loop。  
所以上述例子的执行顺序是：
>**1. console.log(1) 是同步任务，放入主线程里**  
**2. setTimeout() 是异步任务，被放入event table， 0秒之后被推入event queue里**  
**3. console.log(3 是同步任务，放到主线程里**  

当 1、 3在控制条被打印后，主线程去event queue(事件队列)里查看是否有可执行的函数，执行setTimeout里的函数。  

## 在看案例二
```JavaScript
setTimeout(function() {
    console.log('定时器开始')
})；

new Promise(function(resolve) {
    console.log('马上执行for循环')
    for(var i = 0; i < 10000; i++) {
        i == 99 && resolve()
    }
}).then(function() {
    console.log('执行then函数')
})

console.log('代码执行结束')
```
按照案例一的方式分析：

>**1. setTimeout 是异步任务，被放到event table**  
**2. new Promise 是同步任务，被放到主进程里，直接执行打印  console.log('马上执行for循环')**  
**3. .then 里的函数是异步任务，被放到event table**  
**4. console.log('代码执行结束') 是同步代码，被放到主进程里，直接执行**

所以，结果是：`马上执行for循环---代码执行结束---定时器开始---执行then函数`吗?

亲自执行后，结果居然不是这样，而是：`马上执行for循环---代码执行结束---执行then函数---定时器开始`

那么，难道是异步任务的执行顺序，不是前后顺序，而是另有规定？事实上，按照异步和同步的划分方式，并不准确。

而准确的划分方式是：

>**macro-task(宏任务)：包括整体代码script，setTimeout，setInterval**  
**micro-task(微任务)：Promise，process.nextTick**  

{% asset_img 1.jpg 123 %}