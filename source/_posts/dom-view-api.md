title: dom view api
date: 2015-08-27 16:33:33
categories: [前端, js]
tags: [前端, js]
---
记个笔记
http://www.w3.org/TR/2011/WD-cssom-view-20110804/
## 1.window Interface
```Java
partial interface Window {
  MediaQueryList matchMedia(DOMString media_query_list);
  readonly attribute Screen screen;

  // viewport,viewport大小，包括滚动条
  readonly attribute long innerWidth;
  readonly attribute long innerHeight;

  // viewport scrolling
  readonly attribute long scrollX;//和pageXOffset都返回的是Initial Container block左上角到viewport左上角的x偏移
  readonly attribute long pageXOffset;
  readonly attribute long scrollY;
  readonly attribute long pageYOffset;
  void scroll(long x, long y);//将document content的x, y与viewport左上角重合，会触发scroll事件
  void scrollTo(long x, long y);//同scroll
  void scrollBy(long x, long y);//将document content的x偏移 + x, y偏移+ y 与viewport左上角重合，会触发scroll事件

  // client
  readonly attribute long screenX;//距离屏幕左上角的x偏移
  readonly attribute long screenY;
  readonly attribute long outerWidth;
  readonly attribute long outerHeight;
};
```
## 2.Element Interface
```Java
partial interface Element {
  ClientRectList getClientRects();//返回的是元素border box相对viewport的位置
  ClientRect getBoundingClientRect();

  // scrolling
  void scrollIntoView(optional boolean top);
  attribute long scrollTop;   //读取scrollTop时：若元素是根元素document.documentElement，返回window.scrollY；若元素是document.body，文档是quirk模式，body元素没有overflow，则返回window.scrollY；否则返回当前元素content左上角与该元素的content box的左上角在y上的偏移。设置scrollTop时：如果元素是根元素document.documentElement，调用window.scroll(0,y)；若元素是document.body，文档是quirk模式，调用window.scroll(window.scrollX, y)；否则将当前元素content的ele.scrollLeft,y的位置与该元素的content box的左上角对齐
  attribute long scrollLeft;  // scroll on setting
  readonly attribute long scrollWidth;//若元素是document.documentElement且当前不是quirk模式，返回max(document content width, window.innerWidth)；若元素是document.body且文档是quirk模式，返回max(document content width, window.innerWidth)；否则返回padding-left的计算值+padding-right计算值+content width。

  readonly attribute long scrollHeight;

  readonly attribute long clientTop;//border-top-width的计算值（若有滚动条还要加滚动条）
  readonly attribute long clientLeft;//border-left-width的计算值（若有滚动条还要加滚动条）

  readonly attribute long clientWidth;//当元素是document.documentElement时，返回viewport width不包括滚动条。若不是根元素，返回padding-edge（不包含padding-edge和border-edge间的滚动条）,如下图
  readonly attribute long clientHeight;
};
```
![clientWidth](/img/dom-view-api/clientWidth.jpg)

## 3.HTML Element Interface
```Java
partial interface HTMLElement {
  readonly attribute Element offsetParent;//若当前元素是document.documentElement/document.body/position:fixed，返回null；否则是position!=static的最近祖先，如果没有则是body，当前元素position是static祖先是td,th,table
  readonly attribute long offsetTop;//元素padding-edge到offsetParent的border edge
  readonly attribute long offsetLeft;
  readonly attribute long offsetWidth;// 如下图
  readonly attribute long offsetHeight;
};
```
![offsetWidth](/img/dom-view-api/offsetWidth.jpg)
```JavaScript
// chrome
window.innerWidth//1366
document.documentElement.offsetWidth//1349
document.documentElement.clientWidth//1349
// ff
window.innerWidth//1366
document.documentElement.offsetWidth//undefined
document.documentElement.clientWidth//1366
// IE10
window.innerWidth //1366 
document.documentElement.offsetWidth//1366
document.documentElement.clientWidth //1349
```
## 4.MouseEvent Interface
```Java
partial interface MouseEvent {
  readonly attribute long screenX;//到屏幕左上角的x偏移
  readonly attribute long screenY;

  readonly attribute long pageX;//到document content的左上角的x偏移
  readonly attribute long pageY;

  readonly attribute long clientX;//到viewport左上角的x偏移
  readonly attribute long clientY;
  readonly attribute long x;//返回clientX
  readonly attribute long y;

  readonly attribute long offsetX;//到event.target的padding-edge的x偏移
  readonly attribute long offsetY;
};
```