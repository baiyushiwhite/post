title: 1px on retina
categories: [前端, wap]
date: 2015-08-26
tags: [前端, wap, css]
---
## Problem
首先这里的1px是物理像素。在高清屏下，1个CSS像素对应的并不是1个物理像素。
比如iphone4s，屏幕的宽度时320，但是分辨率是640，这就意味着，一个1px的CSS像素会对应4(2x2)个物理像素。
因此要实现1px的物理像素的线条，CSS应该写的是0.5px。然而IOS8之前并不支持这一写法。这就是要解决的问题。
## Solution1
IOS8已经支持0.5px的写法了。但是我们要做个代码检查。
```JavaScript
if (window.devicePixelRatio && devicePixelRatio >= 2) {
  var testElem = document.createElement('div');
  testElem.style.border = '.5px solid transparent';
  document.body.appendChild(testElem);
  if (testElem.offsetHeight == 1) {
    document.querySelector('html').classList.add('hairlines');
  }
  document.body.removeChild(testElem);
}
```
```Css
div {
  border: 1px solid #bbb;
}
 
.hairlines div {
  border-width: 0.5px;
}
```
------
## Solution2
糯米的做法，用的渐变背景做的
```CSS
.retina(@top: transparent, @right: transparent, @bottom: transparent, @left: transparent, @w: 1px) {
    @media only screen and (-webkit-min-device-pixel-ratio: 2),
    only screen and (min-device-pixel-ratio: 2) {
        border: none;
        background-image:
            -webkit-linear-gradient(270deg, @top, @top 50%, transparent 50%),
            -webkit-linear-gradient(180deg, @right, @right 50%, transparent 50%),
            -webkit-linear-gradient(90deg, @bottom, @bottom 50%, transparent 50%),
            -webkit-linear-gradient(0, @left, @left 50%, transparent 50%);
        background-image:
            -moz-linear-gradient(270deg, @top, @top 50%, transparent 50%),
            -moz-linear-gradient(180deg, @right, @right 50%, transparent 50%),
            -moz-linear-gradient(90deg, @bottom, @bottom 50%, transparent 50%),
            -moz-linear-gradient(0, @left, @left 50%, transparent 50%);
        background-image:
            -o-linear-gradient(270deg, @top, @top 50%, transparent 50%),
            -o-linear-gradient(180deg, @right, @right 50%, transparent 50%),
            -o-linear-gradient(90deg, @bottom, @bottom 50%, transparent 50%),
            -o-linear-gradient(0, @left, @left 50%, transparent 50%);
        background-image:
            linear-gradient(180deg, @top, @top 50%, transparent 50%),
            linear-gradient(270deg, @right, @right 50%, transparent 50%),
            linear-gradient(0deg, @bottom, @bottom 50%, transparent 50%),
            linear-gradient(90deg, @left, @left 50%, transparent 50%);
        background-size: 100% @w, @w 100%, 100% @w, @w 100%;
        background-repeat: no-repeat;
        background-position: top, right top, bottom, left top;
    }
}
```
## Solution3
viewport + rem。美团和淘宝是这么做的。
检测到高清屏就将viewport的scale=1/devicePixelRatio。这使得[layout viewport和visual viewport的大小等设备分辨率][1](假设为640px)，因此设置长度设为1px的元素就对应的是1个屏幕像素。然而页面缩小了，所以要将所有长度都按比例扩大。因此设置根元素的font-size*=devicePixelRatio;页面元素设置为rem的都等比例扩大。
## Solution4
transform: scale(0.5)。使用css transform缩放一半的大小。配合:after和:before独立使用较多，比如画一个商品的边框四条线，容器的after和before可以画2条线，利用容器的父元素的after、before再画2条线。
### 单条border
```CSS
.hairlines li {
    position: relative;
    border: none;
}
.hairlines li:after{
    content: '';
    position: absolute;
    left: 0;
    background: #000;
    width: 100%;
    height: 1px;
    -webkit-transform: scaleY(0.5);
            transform: scaleY(0.5);
    -webkit-transform-origin: 0 0;
            transform-origin: 0 0;
}
```
### 多条border
```CSS
.hairlines {
    position: relative;
    margin-bottom: 20px;
    border:none;
}
.hairlines:after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    border: 1px solid #000;
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    width: 200%;
    height: 200%;
    -webkit-transform: scale(0.5);
    transform: scale(0.5);
    -webkit-transform-origin: left top;
    transform-origin: left top;
}
```
## Solution5
box-shadow
```CSS
.shadow {
    -webkit-box-shadow:0 1px 1px -1px rgba(255, 0, 0, 0.5);
    box-shadow:0 1px 1px -1px rgba(255, 0, 0, 0.5);
}
```
  [1]: /