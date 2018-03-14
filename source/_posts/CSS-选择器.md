---
title: CSS 选择器
date: 2018-03-14 17:38:39
tags: CSS
---
CSS选择器的相关笔记

## 元素选择器
可以直接使用html标签作为选择器：
```CSS
h1, h2 {
    border: 3px solid green;
}
p {
    font-size: 14px;
}
```
<!-- more -->

## 类选择器
根据html标签中定义的class名或ID名作为选择器：
```CSS
.nav {
    color: green;
}
/*如果元素有两个类，第二个类紧跟第一个类，中间没有空格*/
.nav.main {
    color: green;
}
/*根据元素ID选择，原则上不能ID名不能重复，但是同名的ID也会正常渲染*/
#nav {
    color: green;
}
```

## 属性选择器
根据html标签的属性选择html元素：
```CSS
[title] {
    color: grey;
}

/*匹配title属性内容为"登录"的元素*/
[title = "登录"] {
    color：grey;
}

/*匹配title属性内容中包含"登录"的元素*/
[title *= "登录"] {
    color: grey;
}

/*匹配title属性开头为"登录"的元素*/
[title ^= "登录"] {
    color: grey;
}
/*匹配title属性结尾为"登录"的元素*/
[title $= "登录"] {
    color: grey;
}
```

## 后代选择器
使用`*`选择当前元素的所有子元素（包含“孙子”元素）
```CSS
.div * {
    padding: 10px;
}

/*选择特定的子元素,按照盒模型顺序依次选择所需子元素，中间加空格；如果div中存在多个同类名的子元素，会默认选中全部同类名子元素
  .div .span {
    padding: 10px;
  }
```

## 相邻选择器（兄弟选择器）
相邻选择器只能从上往下选
```CSS
/* 选择a的下一个相邻元素*/
.a + div {
    margin: 10px;
}

/* 选择a后面的所有同级(兄弟)元素*/
.a ~ div {
    margin: 10px;
}
```

## 伪类选择器
举例：实现一个连接，点击过和没点击过样式不同
```CSS
a:link {
    color: black;
}

a:visited {
    color: blue;
  }
```
>其他伪类选择器'hover' 'active' 'focus'

##  伪元素选择器
举例：首个字母字号非常大
```CSS
p:first-letter {
    font-size: 50px;
}
```
>其他伪元素选择器'after' 'before' 'first-child' 'last-child' 'nth-child()'

## 选择器权重
style属性（内联样式）&gt; ID选择器  &gt; 类选择器、属性选择器、伪类选择器 &gt; 元素选择器

