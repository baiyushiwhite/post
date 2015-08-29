title: tap点透
date: 2015-08-28 12:51:57
categories: [前端, wap]
tags: [前端, wap, js]
---
## 问题
在移动端使用zepto的tap的时候会容易出现点透的现象。比如商场页有一个popup的弹层，用来显示商场地图和信息。弹层右上角有一个关闭弹层的x。当点击弹层的x关闭弹层的时候，会点透到x下面的团单。

## 原因
当手指放在屏幕上时，实际上发生了一系列的事件。touchstart -> touchend -> mouseover -> mousemove -> mousedown -> mouseup -> click触发 click 有明显的 300ms的延迟。之所以有这个延迟是因为浏览器需要确定用户是不是要“双击屏幕” (double-tap)进行缩放。
所以在弹层tap上绑定了隐藏掉弹层的事件，当隐藏 弹层之后，还有一个延迟的 click 事件。但是如果点击的位置上有一个 带有click 事件的元素并且处于页面最前端（离用户最近）或者点击位置的下面是a, input等标签，就会触发这个 click。

## 解决方法
### Fastclick

> FastClick doesn't attach any listeners on desktop browsers.

> Chrome 32+ on Android with width=device-width in the viewport meta tag doesn't have a 300ms delay, therefore listeners aren't attached.

> <meta name="viewport" content="width=device-width, initial-scale=1">
> Same goes for Chrome on Android (all versions) with user-scalable=no in the viewport meta tag. But be aware that user-scalable=no also disables pinch zooming, which may be an accessibility concern.

> For IE11+, you can use touch-action: manipulation; to disable double-tap-to-zoom on certain elements (like links and buttons). For IE10 use -ms-touch-action: manipulation.

```JavaScript
document.addEventListener('DOMContentLoaded', function() {
    FastClick.attach(document.body);
}, false);
```
（1）FastClick 是在 touchend 的时候，模拟触发该元素的click事件。
会返回一个事件，每个返回的事件会有一个自己的特定的init方法，比如initMouseEvent的方法，调用该方法可以初始化事件对象。
```JavaScript
var clickEvent = document.createEvent('MouseEvent')
clickEvent.initMouseEvent(
    'click',//事件类型
    true,//是否冒泡
    true,//是否可以取消
    window,//相关联的视图document.defaultView
    1,//与事件相关的详细信息
    touch.screenX,//事件相对于屏幕的位置
    touch.screenY,//
    touch.clientX,//相对viewport的位置
    touch.clientY,
    false,//是否按下了crl
    false,//是否按下了alt
    false,//是否按下了shift
    false,//是否按下了meta
    0,//按下了鼠标的哪个键
    null
);
clickEvent.isMyOwnEvent = true;
target.dispatchEvent(clickEvent)
```
（2）然后在document.body上监听真正的click事件，在真正的 click 到来的时候，会首先在捕捉阶段在Fastclick.layer, 这里是 document.body 中捕获，如果不是自己定义的click事件，则通过 e.stopPropagation()阻止后续节点对 click 的事件处理，并通过 e.stopImmediatePropagation() 阻止我们在 
Fastclick.layer 上添加的其他回调。通过preveneDefault();来组织事件的默认行为。
```JavaScript
document.body.addEventListener('click', function (e) {
    if (e.isMyOwnEvent) {
        // 模拟的click事件
        return;
    }
    // 真正的click事件
    e.stopImmediatePropagation();
    e.stopPropagation();
    e.preventDefault();
}, true);// 捕捉阶段就阻止真正的事件
```
http://jsbin.com/hewobi/2/edit?html,js,output
### 引用
https://github.com/ftlabs/fastclick

