#web前端异步流程控制

前端发展的今天，对于产品工程化的要求也越来越高，一个最基本的诉求就是开发出的组件、插件必须要是低耦合，便于后续维护的。常见方式就是按照MVC的结构进行业务分离，这里就涉及到异步控制的概念。
    本篇文章就适合现有项目筛选出的异步控制工具进行简单的操作说明，及常用API介绍
	
**1.jQuery**
公司现有的项目中，基本以Vue为核心进行开发，但是依然少不了的DOM操作而引入的jQuery库，如果项目中对于异步的需求并不复杂的话，完全无需再引入别的异步控制库，jquery中的延迟对象完全可以胜任这方面工作，调用如下：
`$.deferred()`
>描述: 一个构造函数，返回一个链式实用对象方法来注册多个回调，回调队列，  调用回调队列，并转达任何同步或异步函数的成功或失败状态。

可以暂时理解为生成一个新的延迟对象，并切换延迟对象执行的结果（有三种状态，开始时为pending，执行成功为resolved，执行失败为rejected，状态变更后分别调用自身指定的回调函数）
```
$.deferred(function(){
    // 要执行的函数，当前状态为pending
})
.done(function(){
    // 状态变更为resolved时的回调
})
.fail（function(){
    // 状态变更为rejected时的回调
})
.always(function(){
    // 只要状态变更就调用
})
```
还有一个会常用的API是多个并行的异步
```
$.when($.get('...'),$.post('...')）
// 可以传入多个延迟对象
```
>有多个延迟对象传递给jQuery.when时，该方法返回一个新的“宿主”延迟对象，跟踪所有已通过Deferreds聚集状态。 当所有的延迟对象被受理（resolve）时，该方法才会受理它的 master 延迟对象。当其中有一个延迟对象被拒绝（rejected）时，该方法就会拒绝它的 master 延迟对象。

意思是说，传入的多个延迟对象，均处于resolved时，才触发成功的回调。如果有一个状态变为rejected时，则触发失败的回调

查看API详细内容参考[jQuery中文文档](http://www.jquery123.com/category/deferred-object/)

**2.其他第三方库（async）**

拿Vue来说，原则上是能不引入jquery操作DOM最好，在不使用jquery时，就需要考虑其他第三方的库，比较github上其他的异步控制库。成熟，健壮的异步流程控制库有很多，co、then、async等等；
[then.js](https://github.com/teambition/then.js)：一个国人写的异步控制库，回调转换为链式写法，同时可以在node及浏览器环境下使用，并且支持到IE6、7
 [async.js](https://github.com/caolan/async)：是github上star最多的异步控制库，同样支持node及浏览器环境（IE9+）
==tips：then.js 异步控制为链式写法，async则为回调的形式（技术选型的时候要注意这点） #800000==

本例以async的使用做简单API介绍
Async主要包含了三大部分的内容分别是：

 1. Collections: 如何使用异步操作处理集合中的数据；
 2. Control Flow: 常见的流程处理，也是本篇重点内容；
 3. Utils:一些常用工具方法。

Control Flow中常用API介绍（详细文档信息参照[Async官方文档](https://caolan.github.io/async/)）

1.async.series
```
// 此方法为串联的形式，根据顺序依次执行，如果运行时出现错误，不再往下执行，并立即执行callback
async.series([
    function(callback) {
        if(resolved){
            callback(null, data);   // 这里的意思是成功后将信息传递过去（如果你需要在callback里面处理传递的参数）
        }else if(rejected){
            callback(data);     // 其中这里的data可以是任意数据类型，function也是合法的
        }
    },
    function(callback) {
        // ....处理同上
    }
],
// 可选项，完成后的回调函数（不管成功或者失败都会调用，在callback内部进行成功/失败的处理）
function(err, results) {
    err()   // 如果是函数直接运行
    console.log(err) //如果传递的数据，输出错误的数据，这都是合法的
});
```
2.async.parallel
```
// 此方法为并联执行，无需按顺序，而是同时进行,其中一个函数错误不影响其他函数运行 
async.parallel([
    function(callback) {
        setTimeout(function() {
            callback('one');    // 在这里如果是运行失败，是把失败的信息传递给了callback，而不是直接调用callback，全部完成时候才调用callback
        }, 200);
    },
    function(callback) {
        setTimeout(function() {
            callback(null, 'two');
        }, 100);
    }
],
function(err, results) {
    // 在这里检测失败或者成功的信息
});
```
3.async.waterfall
```
// 串联运行，并且每个函数将其结果传递给下一个函数，如果运行时错误，则下一个函数不再执行，callback将会被调用
async.waterfall([
    function(callback) {
        setTimeout(function() {
            callback(null, 'one', 'two');
        }, 200);
    },
    function(arg1, arg2, callback) {
        // 这里的arg1，arg2 分别是是上面函数传递的'one'，'two'
        setTimeout(function() {
            callback(null, 'three');
        }, 200) 
    },
    function(arg1, callback) {
        setTimeout(function() {
            callback(null, data);   // 这里的data传递到了callback中的result中
        }, 200)
    }
], function (err, result) {
    // 处理错误/成功的信息
});
```
4.async.whilst
```
// 类似轮询一样。 首先执行第二个函数，执行成功后，对比条件（第一个函数），如果不满足，会继续执行自身，知道满足条件（第一个函数），期间如果第二个函数失败的话，中断，立即执行callback
var count = 0;
async.whilst(
    function() { return count < 5; },
    function(callback) {
        count++;
        setTimeout(function() {
            callback(null, count);
        }, 1000);
    },
    // 可选的回调
    function (err, n) {
        // 5 seconds have passed, n = 5
    }
);
```
