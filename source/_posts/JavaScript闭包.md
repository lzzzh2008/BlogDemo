---
title: JavaScript闭包
date: 2017-12-19 17:31:07
tags: JavaScript
---
看了阮一峰老师的闭包教程，有一个非常不错的例子来学习闭包和this关键字
阮老师博文地址<http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html>  

# 关于闭包 #
> 闭包就是能够读取其他函数内部变量的函数。
> 由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成"定义在一个函数内部的函数"。  以下代码中`f2`就是一个典型的闭包。
```JavaScript
function f1(){

　　　　var n=999;

　　　　function f2(){
　　　　　　alert(n);
　　　　}

　　　　return f2;

　　}

　　var result=f1();

　　result(); // 999
```
<!-- more -->
先来看两个代码片段
#### 片段一 ####
```JavaScript
var name = "The Window";
　　var object = {
　　　　name : "My Object",
　　　　getNameFunc : function(){
　　　　　　return function(){
　　　　　　　　return this.name;
　　　　　　};
　　　　}
　　};

alert(object.getNameFunc()());
```
执行结果是 * The Window *

### 片段二 ###
```JavaScript
var name = "The Window";
　　var object = {
　　　　name : "My Object",
　　　　getNameFunc : function(){
　　　　　　var that = this;
　　　　　　return function(){
　　　　　　　　return that.name;
　　　　　　};
　　　　}
　　};
alert(object.getNameFunc()());
```
执行结果是 * My Object *

# 分析 #
&emsp;&emsp;在片段一中，`object.getNameFunc()`返回了一个函数（这个函数并没有被执行），而这个返回的函数构成了闭包。`object.getNameFunc()()`第二个`()`执行了返回的函数，由于闭包，此时是`Window`调用闭包函数，也就是说此时`this`指向的是`window`。

&emsp;&emsp;在片段二中，`object.getNameFunc()`的`var that = this;`被执行（此时this指向Object），执行闭包时的`that.name`指的就是`object`中的`name`，所以结果是`My Object`
