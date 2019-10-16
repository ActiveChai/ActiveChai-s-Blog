---
title: 不同设备上网页的适配
date: 2019-08-17 14:33:00
tags: [网页适配]
categories:
- 网站建设
---
面对不同分辨率的设备，我们该如何开发网页？<!-- more -->

此笔记源于实习期间的一次分享。

# PC端网页的适配

>适配目标
>- 在不同分辨率的电脑上，网页可以正常显示
>- 放大或者缩小浏览器窗口，网页可以正常显示
>
>关键点
>- 宽度的适配

<img src="http://res.activechai.cn/webadapter/1.jpg">

将页面分解为背景层（一般宽度都会大于1200px）和内容层（一般宽度都会小于1200px）
-- 背景层：背景纯色、背景图片
-- 内容层：主体内容都封装在盒子内，保持居中

## ①主体内容宽度等于窗口宽度

<img src="http://res.activechai.cn/webadapter/2.jpg">

-- 流式布局（百分比布局）
   一个元素在原设计稿里，距离左边是 200px，如果写死可能是 left: 200px 或者 margin-left: 200px。
   现在要转成百分比，那么这个值可能就是 20%。这个值怎么算？如果设计稿是1000，200/1000=20%。

## ②主体内容宽度先小于窗口宽度，后等于窗口宽度

<img src="http://res.activechai.cn/webadapter/3.png">

-- 响应式布局
使用同一套 HTML，通过 CSS Media Queries 等技术来判断屏幕分辨率大小，  然后根据相应的分辨率使用相应的 CSS 样式。

<img src="http://res.activechai.cn/webadapter/4.png">

### CSS Media Queries（CSS 媒体查询）

```HTML
第一种写法：
<!-- 分辨率低于1280，采用 1.css 样式表 -->
<link rel="stylesheet" media="(max-width: 1280px)" href="1.css"/>

<!-- 分辨率高于1400，采用 2.css 样式表 -->
<link rel="stylesheet" media="(min-width: 1400px)" href="2.css"/>

第二张写法：
<style>
/* 分辨率低于1280，采用下面的样式 */
@media (max-width: 1280px) {
  div {
    width: 200px;
    height: 200px;
    background-color: green;
  }
}

/* 分辨率高于1400，采用下面的样式 */
@media (min-width: 1400px) {
  div {
    width: 300px;
    height: 300px;
    background-color: red;
  }
}
</style>
```

<img src="http://res.activechai.cn/webadapter/5.png">

## ③主体内容宽度先小于窗口宽度，后大于窗口宽度

<img src="http://res.activechai.cn/webadapter/6.png">

-- 静态布局
网页主体内容有固定的大小，超出宽高的部分用滚动条(overflow: scroll)来实现滚动查阅。

# 移动端网页的适配

## ①视口 viewport

PC 端：浏览器可视区域的大小
移动端：
-- layoutviewport：大于实际屏幕，用于保证网站的外观特性与桌面浏览器一样。
-- visualviewport: 当前显示在屏幕上的页面，即浏览器可视区域的宽度。
-- idealviewport:  设备视口宽度，移动设备的理想 viewport，固定不变。

<img src="http://res.activechai.cn/webadapter/7.png">

## ②viewport的设置

```HTML
<meta name="viewport" content="width=device-width, initial-scale=1, user-scale=no" />
```

width=device-width：width 设置的是 layoutviewport 的宽度，宽度等于当前设备宽度。
initial-scale=1：initial-scale 设置页面的初始缩放值。
user-scalabel=no：用户是否可以手动缩放，默认设置为 no 。
只要 layoutviewport === visualviewport，页面下面不会出现滚动条，默认只是把页面放大或缩小。

为什么要设置 viewport？
①媒体查询 @media 响应式布局中，会根据媒体查询功能来适配多端布局（width）。
②为了产出高保真页面，通过设置 viewport 的参数来进行整体换算。

## ③高清方案

设备像素比（dpr）= 物理像素 / 设备独立像素 
-- 物理像素：就是我们经常所说的分辨率，如 iPhone 6 的分辨率就是 750x1334
-- 独立像素：就是手机的实际视窗，如 iPhone 6 的视窗就是 375x667
所以 iPhone 6 的设备像素比 = 750 / 375 = 2
当 dpr = 1（也就是最小值）时，那么它普通显示屏，
当 dpr > 1（通常是1.5、2.0）时，那么它就是高清显示屏。

dpr=2 --> initial-scale 设置为 0.5=1/2 
dpr=3 --> initial-scale 设置为 0.33=1/3

<img src="http://res.activechai.cn/webadapter/8.png">

## ④rem 的适配方案

rem 布局的本质是等比缩放，一般是基于宽度。
rem 设置：rem 是相对于根元素 html 的 font-size 来做计算。
通常在页面初始化时加载时通过对 document.documentElement.style.fontSize 设置来实现。
现在大部分浏览器 IE9+，Firefox、Chrome、Safari、Opera ，如果我们不修改相关的字体配置，都是默认显示 font-size 是 16px，即

```CSS
html {
    font-size: 16px;  // 1rem = 16px（默认）
}
```

为了使网页适配设配，在不同的设备比例相同，我们可以根据屏幕宽度动态设置根元素 html 的 font-size。
举个例子：物理像素为 clientWidth，若屏幕等分为 10 份，那么可以设置 font-size（1rem）为

```JavaScript
document.documentElement.style.fontSize = document.documentElement.clientWidth / 10 + 'px';
```

<img src="http://res.activechai.cn/webadapter/9.png">

# 同一网页在PC端和移动端的适配

## ①响应式

使用同一套 HTML，通过 CSS Media Queries 等技术来判断屏幕分辨率大小，然后根据相应的分辨率使用相应的 CSS 样式。

## ②自适应

为不同类别的设备建立不同的网页，检测到设备分辨率大小后调用相应的网页。
通常是使用URL前缀不同来相应不同的适配效果。
举例：用电脑和手机浏览器分别访问京东、淘宝、百度等。

<img src="http://res.activechai.cn/webadapter/10.png">
