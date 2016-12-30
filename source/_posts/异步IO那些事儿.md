---
title: 异步IO那些事儿
date: 2016-08-10 20:11:43
tags: JavaScript
categories: Tech
description: 什么是回调？你到一个商店买东西，刚好你要的东西没有货，于是你在店员那里留下了你的电话，过了几天店里有货了，店员就打了你的电话，然后你接到电话后就到店里去取了货。在这个例子里，你的电话号码就叫回调函数，你把电话留给店员就叫登记回调函数，店里后来有货了叫做触发了回调关联的事件，店员给你打电话叫做调用回调函数，你到店里去取货叫做响应回调事件。

---

## 1.什么是回调？

> 你到一个商店买东西，刚好你要的东西没有货，于是你在店员那里留下了你的电话，过了几天店里有货了，店员就打了你的电话，然后你接到电话后就到店里去取了货。在这个例子里，你的电话号码就叫回调函数，你把电话留给店员就叫登记回调函数，店里后来有货了叫做触发了回调关联的事件，店员给你打电话叫做调用回调函数，你到店里去取货叫做响应回调事件。

From:[https://www.zhihu.com/question/19801131/answer/13005983](https://www.zhihu.com/question/19801131/answer/13005983)
以下用代码验证我的理解。

```
function learn(something) {
    console.log(something);
}

function we(callback, something) {
    something += ' is cool';
    callback(something);
}

we(learn, 'Node'); // Node is cool

```

[理解与使用回调函数](http://www.html-js.com/article/Sexy-Javascript-understand-the-callback-function-with-the-use-of-Javascript-in)

## 2.什么是同步/异步？

### 同步

一个web页面中，JS代码都是按照标签顺序来执行的。

```html
// 例1
<script src="file.js></script>
<img src="image.jpg" /> //图片多快出现在你眼前，取决于file.js的装载速度
```

```javascript
// 例2
var c = 0;

function printIt() {
    console.log(c);
}

function plus() {
    c += 1;
}

plus();
printIt(); // 1
```

### 异步

> 异步处理不用阻塞当前线程来等待处理完成，而是允许后续操作，直至其它线程将处理完成，并回调通知此线程。

举个例子，你和女票今晚想吃小龙虾，就打开手机订外卖，订完就等着外卖小哥打你电话了。外卖小哥打你电话相当于触发回调函数。这个就是`异步处理`。
以下为未进行回调操作的代码

```javascript
var c = 0;

function printIt() {
    console.log(c);
}

function plus() {
    setTimeout(function() {
        c += 1;
    }, 1000);
}

plus();
printIt(); // 0
```

进行回调：

```javascript
var c = 0;

function printIt() {
    console.log(c);
}

function plus(callback) {
    setTimeout(function() {
        c += 1;
        callback();
    }, 1000);
}

plus(callback); // 1
```

## 3.什么是单线程/多线程？

### 单线程

单线程在程序执行时，所走的程序路径按照连续顺序排下来，前面的必须处理好，后面的才会执行。JavaScript就是单线程的：

```javascript
var isEnd = true;
     window.setTimeout(function () {
         isEnd = false;//1s后，改变isEnd的值
     }, 1000);
     //这个while永远的占用了js线程，所以setTimeout里面的函数永远不会执行
     while (isEnd);
     //alert也永远不会弹出
     alert('end');
```

### 多线程

多线程即在同一时间处理多个任务。就像一个人脚踏两条船，在一个时间段交两个女票（？？？？）。
[JavaScript多线程方案——Web worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers);

## 4.什么是阻塞/非阻塞？阻塞/非阻塞与同步/异步的关系。

还是订外卖的例子：

1. 点完菜干等着啥也不干，在门口等菜来了你自己取菜吃。—阻塞、同步
2. 点完菜在家写代码，在门口等菜来了你自己取菜吃。—非阻塞、同步
3. 点完菜干等着啥也不干，等外卖小哥打你电话你出门收菜吃。—阻塞、异步
4. 点完菜在家写代码，等外卖小哥打你电话你出门收菜吃。—非阻塞、异步

## 5.什么是事件循环？

> 主线程从”任务队列”中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。

[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
[JS事件循环详解](http://web.jobbole.com/83360/)