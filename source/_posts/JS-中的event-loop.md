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

{% asset_img 1.jpg 流程图 %}  
按照这种分类方式，JS的执行机制是：  
>**1. 执行一个宏任务，过程中如果遇到微任务，就将其放到微任务的“事件队列”里**  
**2. 当前宏任务执行完成后，会查看微任务的“事件队列”，并将里面全部的微任务依次执行完**  
**3. 重复以上2步骤，结合event loop(1) event loop(2)，就是更为准确的JS执行机制了**   

尝试按照刚学的执行机制，去分析例2：  


>**1. 首先执行script下的宏任务，遇到setTimeout,将其放到宏任务的“队列”里**  
**2. 遇到 new Promise直接执行，打印"马上执行for循环"**  
**3. 遇到then方法，是微任务，将其放到微任务的“队列”里。**  
**4. 打印 "代码执行结束"**  
**5. 本轮宏任务执行完毕，查看本轮的微任务，发现有一个then方法里的函数，打印"执行then函数"**  
**6. 到此,本轮的event loop 全部完成。**  
**7. 下一轮的循环里，先执行一个宏任务，发现宏任务的“队列”里有一个setTimeout里的函数,执行打印"定时器开始"**  

所以最后的执行顺序是： `马上执行for循环---代码执行结束---执行then函数---定时器开始`

## 关于setTimeout
这段`setTimeout`代码什么意思? 我们一般说: 3秒后,会执行`setTimeout`里的那个函数
```JavaScript
setTimeout(function () {
    console.log('执行了')
}, 3000)
```
但是这种说并不严谨，准确的解释是：3秒后，`setTimeout`里的函数被会推入event queue，而event queue(事件队列)里的任务，只有在主线程空闲时才会执行。

>所以只有满足 (1)3秒后 (2)主线程空闲，同时满足时，才会3秒后执行该函数

如果主线程执行内容很多，执行时间超过3秒，比如执行了10秒，那么这个函数只能10秒后执行了。