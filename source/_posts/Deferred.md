title: Deferred
categories: [前端, js]
date: 2015-08-27
tags: [前端, js]
---
- 首先是一个对象 object；
- 是对Promise/A规范的实现：http://promisesaplus.com/
- 管理异步操作中回调函数的一个对象；
- 将异步操作包装成具有同步操作特性；
- 改变了异步操作中回调函数组织方式；由层层嵌套的组织方式(厄运的金字塔)，变成了一种优雅的链接方式，提高了异步操作中代码的可读性、耦合性、维护性；

## 1. 新建一个deferred对象
```JavaScript
var dtd = $.Deferred(); //新建一个Deferred对象
var dtd = $.Deferred(function(d) {
    //do something
})//将把$.Deferred()创建的默认的deferred对象传递给fn
```
## 2. deferred对象基本知识
 - deferred对象三种状态：pending、resolved、rejected
 - 注册回调函数的方法：dfd.done()、dfd.fail()、dfd.always()、dfd.progress()、dfd.then()
 - trigger回调函数的三类方法：dfd.resolve()/dfd.resolveWith()、dfd.reject()/dfd.rejectWith()、dfd.notify()/dfd.notifyWith()加with就可以指定回调函数调用的上下文
 - deferred.then( doneFilter [, failFilter ] [, progressFilter ] )：
 (1)当dfd处于resolved时，执行doneFilter；
 (2)当dfd处于rejected时，执行failFilter；
 (3)当dfd调用notify或notifyWith时，执行progressFilter
 - 当deferred处于resolved状态或者rejected状态时deferred.always(alwaysCallbacks [, alwaysCallbacks ])执行
 - 检测dfd状态方法：dfd.state()、dfd.isResolved()、dfd.isRejected()

## 3. 关于then
 - jQuery1.8之前，dfd.then()等价于dfd.done().fail()
 - jQuery1.8+
（1）then()返回一个新的promise对象 (deferred对象的只读版本，不能改变他的状态，即没有resolve、reject方法）
（2）如果then()指定的回调函数有返回值，该返回值会作为参数，传入后面的回调函数。当then的返回值是一个derferred对象时，后面的注册方法都是注册在新的deferred对象上。
```JavaScript
var defer1 = $.Deferred(); //构建异步对象 
defer.then(function(value) {
    alert('derfer1 ' + value);
    var defer2 = $.Deferred();
    setTimeout(function () {
        defer2.resolve(value * 5);
    }, 2000); 
    return defer2;
}).done(function (value) {
    alert('打印出值 ' + value); //将过5秒再打印出25
});

setTimeout(function () {
    defer.resolve(5);
}, 1000); 
```
所以通过这个可以用来重写多个嵌套的ajax请求
```JavaScript
// 之前这样的代码
$.get('js/js1', function () {
    //do something
    $.get('js/js2', function () {
        //do something
        $.get('js/js3', function () {
            //do something
            $.get('js/js4', function () {
                //do something
                $.get('js/js5.js', function () {
                    //do something
                });
            });
        });
    });
});
// 可以改写成
$.get('js/js1').then(function () {
    return $.get('js/js2');
}).then(function () {
    return $.get('js/js3');
}).then(function () {
    return $.get('js/js4');
}).then(function () {
    return $.get('js/js5');
}).then(function () {
    doSomething();
});
```
## 4. $.when()
传入多个deferred指定，为多个事件指定回调函数，只有全部deferred都resolve了才调用then的第一个参数。如果要等待多个异步事件完成才触发某一个事件，那么这个方法非常有用。
```JavaScript
var dfd1 = $.Deferred();
var dfd2 = $.Deferred();
var dfd3 = $.Deferred();
$.when(dfd1, dfd2, dfd3).then(function(){
    console.log('ok');
}, function() {
    console.log(‘someone error’);
});
dfd1.resolve();
dfd2.resolve();
dfd3.resolve();
```


