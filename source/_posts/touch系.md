title: touch系
date: 2015-08-28 14:51:57
categories: [前端, wap]
tags: [前端, wap, js]
---
记点笔记
http://www.w3.org/TR/touch-events/
## 1.touch事件类型
![事件类型](/img/touch系/touchtype.jpg)
## 2.TouchEvent Interface
```Java
interface TouchEvent : UIEvent {
      readonly  attribute TouchList touches;//当前位于屏幕上的所有触点列表
      readonly  attribute TouchList targetTouches;//位于当前 DOM 元素上的触点列表
      readonly  attribute TouchList changedTouches;//涉及当前事件的触点列表。touchstart事件：当前事件所触到的点；touchmove：从上次事件到此时触到的所有点；touchend和touchcancel：刚从表面离开的点
      readonly  attribute boolean   altKey;
      readonly  attribute boolean   metaKey;
      readonly  attribute boolean   ctrlKey;
      readonly  attribute boolean   shiftKey;
};
```
## 3.TouchList Interface
TouchList就是一个Touch对象的列表，一般在用户多个手指同时接触触控平面时使用这个接口
```Java
interface TouchList {
    readonly  attribute unsigned long length;
    getter Touch? item (unsigned long index);
};
```

## 4.Touch Interface
Touch对象是只读的，一旦创建就不会改变了
```Java
interface Touch {
      readonly    attribute long        identifier;
      readonly    attribute EventTarget target;
      readonly    attribute long        screenX;//到屏幕x偏移
      readonly    attribute long        screenY;
      readonly    attribute long        clientX;//到viewport x偏移
      readonly    attribute long        clientY;
      readonly    attribute long        pageX;//到document content x偏移
      readonly    attribute long        pageY;
  };
```
## 5.Extensions to the Document Interface
```Java
partial interface Document {
      Touch createTouch (WindowProxy view, EventTarget target, long identifier, long pageX, long pageY, long screenX, long screenY);//创建一个touch事件
      TouchList createTouchList (Touch... touches);
  };
```
参考：
http://www.html5rocks.com/zh/mobile/touch/
https://developer.mozilla.org/zh-CN/docs/Web/API/Touch_events#.E5.A4.84.E7.90.86.E5.8F.96.E6.B6.88.E8.A7.A6.E6.91.B8.E4.BA.8B.E4.BB.B6