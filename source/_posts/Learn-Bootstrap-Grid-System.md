---
title: Learn Bootstrap Grid Systems
date: 2016-12-18 19:05:14
tags: [css,bootstrap]
categories: Tech
description: Bootstrap 提供了一套响应式、移动设备优先的流式栅格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为最多12列...

---

> **栅格设计系统**（又称**网格设计系统**、**标准尺寸系统**、**程序版面设计**、**瑞士平面设计风格**、**国际主义平面设计风格**），是一种[平面设计](https://zh.wikipedia.org/wiki/%E5%B9%B3%E9%9D%A2%E8%AE%BE%E8%AE%A1)的方法与风格。运用固定的格子设计版面布局，其风格工整简洁，在二战后大受欢迎，已成为今日出版物设计的主流风格之一。

Bootstrap 提供了一套响应式、移动设备优先的流式栅格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为最多12列。包含了用于简单的布局选项的预定义类，也包含了用于生成更多语义布局的功能强大的混合类。

学习栅格系统有助于更好地理解响应式布局。

栅格系统的使用方法具体不做介绍，详细可见 Bootstrap 文档。

## Bootstrap 中的栅格

### 容器 Container

容器的主要作用就是规定整个栅格系统的宽度，源码如下。

``` css
.container {
  padding-right: 15px;
  padding-left: 15px;
  margin-right: auto;
  margin-left: auto;
}
@media (min-width: 768px) {
  .container {
    width: 750px;
  }
}
@media (min-width: 992px) {
  .container {
    width: 970px;
  }
}
@media (min-width: 1200px) {
  .container {
    width: 1170px;
  }
}
```

从上面的源码可以看出，容器的宽度已经是响应式的了。而且有了容器之后，栅格系统的宽度并不是100%全屏显示的了。这里容器还设置了内边距，我们可以先看 Row 和 Column 的源码再来看看容器为什么要设置内边距。

### 行 Row

首先，行的外边距设置为-15px。

``` css
.row {
  margin-right: -15px;
  margin-left: -15px;
}

.row:before,
.row:after {
  display: table;
  content: " ";
}

.row:after {
  clear: both;
}
```

栅格中，行嵌套在容器中，而列（Columns）又包含在行中。行的主要目的就是确保其中的列不会到外面去，这里通过清除浮动来解决。可是，清除浮动为什么要这么写？其实这是一个`clearfix hack`(css 真是一门博大精深的语言。。。)，详见http://nicolasgallagher.com/micro-clearfix-hack/

### 列 Column

``` css
.col-xs-1, .col-sm-1, .col-md-1, .col-lg-1, .col-xs-2, .col-sm-2, .col-md-2, .col-lg-2, .col-xs-3, .col-sm-3, .col-md-3, .col-lg-3, .col-xs-4, .col-sm-4, .col-md-4, .col-lg-4, .col-xs-5, .col-sm-5, .col-md-5, .col-lg-5, .col-xs-6, .col-sm-6, .col-md-6, .col-lg-6, .col-xs-7, .col-sm-7, .col-md-7, .col-lg-7, .col-xs-8, .col-sm-8, .col-md-8, .col-lg-8, .col-xs-9, .col-sm-9, .col-md-9, .col-lg-9, .col-xs-10, .col-sm-10, .col-md-10, .col-lg-10, .col-xs-11, .col-sm-11, .col-md-11, .col-lg-11, .col-xs-12, .col-sm-12, .col-md-12, .col-lg-12 {
  position: relative;
  min-height: 1px;
  padding-right: 15px;
  padding-left: 15px;
}

/** 设置列的宽度、使列向左浮动的css代码 **/
```

列现在也有15px的 padding，而容器本身具有15px的 padding，行用-15px的 margin 反向延伸回去，所以现在列的 padding 部分与容器的 padding 部分重合了，我们可以画个图感受感受。如图（图是盗的。。。）： 

![img](https://segmentfault.com/image?src=http://www.helloerik.com/wp-content/uploads/image-3.png&objectId=1190000000743553&token=8fe037cc7d1cf17194a0a699a326c17c)

然后列的内容与容器之间就有了15px的距离，而列内容与列内容之间就有了30px的gutter。这时，也可以继续在列里面嵌套栅格系统，因为此时列也可以当作容器。

### 响应式

响应式处理主要通过媒体查询来设置列的不同宽度，Bootstrap 根据不同的设备尺寸设置了不同的类前缀：

![](http://o6ljw8wcq.bkt.clouddn.com/20161218215521.png)

我们继续看列的剩下的代码，以平板（≥768px）大小为例：

``` css
@media (min-width: 768px) {
  .col-sm-1, .col-sm-2, .col-sm-3, .col-sm-4, .col-sm-5, .col-sm-6, .col-sm-7, .col-sm-8, .col-sm-9, .col-sm-10, .col-sm-11, .col-sm-12 {
    float: left;
  }
  .col-sm-12 {
    width: 100%;
  }
  .col-sm-11 {
    width: 91.66666667%;
  }
  .col-sm-10 {
    width: 83.33333333%;
  }
  .col-sm-9 {
    width: 75%;
  }
  .col-sm-8 {
    width: 66.66666667%;
  }
  .col-sm-7 {
    width: 58.33333333%;
  }
  .col-sm-6 {
    width: 50%;
  }
  .col-sm-5 {
    width: 41.66666667%;
  }
  .col-sm-4 {
    width: 33.33333333%;
  }
  .col-sm-3 {
    width: 25%;
  }
  .col-sm-2 {
    width: 16.66666667%;
  }
  .col-sm-1 {
    width: 8.33333333%;
  }
```

Bootstrap 的栅格系统把每一行分成12部分，每一列横跨n个部分就占比 n/12 * 100%，这个规则对列来讲都是固定不变的，因为外围的容器已经针对不同尺寸做好自适应宽度了，列宽的参照物即容器宽。



参考资料：

1. [The Subtle Magic Behind Why the Bootstrap 3 Grid Works](http://www.helloerik.com/the-subtle-magic-behind-why-the-bootstrap-3-grid-works)

2. [Micro Clearfix Hack](http://nicolasgallagher.com/micro-clearfix-hack/)

   ​