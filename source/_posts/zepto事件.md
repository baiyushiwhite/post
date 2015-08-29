title: zepto事件
date: 2015-08-28 13:45:59
categories: [前端, wap]
tags: [前端, wap, js]
---
zepto简单封装了几个常用的事件。源码只有这么一点点：
```JavaScript
$(document).ready(function(){
    var now, delta

    $(document.body).bind('touchstart', function(e){
      now = Date.now()
      delta = now - (touch.last || now)
      touch.el = $(parentIfText(e.touches[0].target))
      touchTimeout && clearTimeout(touchTimeout)
      touch.x1 = e.touches[0].pageX
      touch.y1 = e.touches[0].pageY
      if (delta > 0 && delta <= 250) touch.isDoubleTap = true
      touch.last = now
      longTapTimeout = setTimeout(longTap, longTapDelay)
    }).bind('touchmove', function(e){
      cancelLongTap()
      touch.x2 = e.touches[0].pageX
      touch.y2 = e.touches[0].pageY
    }).bind('touchend', function(e){
       cancelLongTap()

      // double tap (tapped twice within 250ms)
      if (touch.isDoubleTap) {
        touch.el.trigger('doubleTap')
        touch = {}

      // swipe
      } else if ((touch.x2 && Math.abs(touch.x1 - touch.x2) > 30) ||
                 (touch.y2 && Math.abs(touch.y1 - touch.y2) > 30)) {
        touch.el.trigger('swipe') &&
          touch.el.trigger('swipe' + (swipeDirection(touch.x1, touch.x2, touch.y1, touch.y2)))
        touch = {}

      // normal tap
      } else if ('last' in touch) {
        touch.el.trigger('tap')

        touchTimeout = setTimeout(function(){
          touchTimeout = null
          touch.el.trigger('singleTap')
          touch = {}
        }, 250)
      }
    }).bind('touchcancel', function(){
      if (touchTimeout) clearTimeout(touchTimeout)
      if (longTapTimeout) clearTimeout(longTapTimeout)
      longTapTimeout = touchTimeout = null
      touch = {}
    })
  })

  ;['swipe', 'swipeLeft', 'swipeRight', 'swipeUp', 'swipeDown', 'doubleTap', 'tap', 'singleTap', 'longTap'].forEach(function(m){
    $.fn[m] = function(callback){ return this.bind(m, callback) }
  })
```
从以上逻辑可以看出来，每个事件的实现逻辑如下，

 - longTap：touchStart750ms之内没有触发touchMove和touchEnd
 - doubleTap: 250ms之内触发了两次touchStart
 - swipe：touchStart后有touchMove发生，且最后的移动距离超过30，则触发swipe事件；根据上下偏移的距离和左右偏移的距离，大的为最后swipe的方向。再触发swipeLeft,swipeRight, swipeUp, swipeDown事件。
 - tap:在tap之后会延迟250触发一个singleTap事件来判断是否进行了两次touchStart。如果在250ms之内又触发了一个touchStart则会清空singleTap的延迟调用。
