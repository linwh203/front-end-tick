## 对于’promise’,’setTimeout’等函数混合出现时候的运行顺序问题，我们都知道这些异步的方法会在当前任务执行结束之后调用，但为什么’promise’会在’setTimeout’之前执行？ 具体的实现原理是什么？

# 《Tasks, microtasks, queues and schedules》

### link: https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/?utm_source=html5weekly

## js异步实现原理

  关于Event Loop，js是单线程的，而实现异步主要就是通过将异步的内容压入tasks，当前任务执行结束之后，再执行tasks中的callback。

  Tasks是一个任务队列，Js在执行同步任务的时候，只要遇到了异步执行和函数，都会把这个内容压入Tasks中，然后在当前JsStack中的同步任务完成后，再去Tasks中执行相应的回调。 比如代码中的setTimeout，当遇到这个函数，总会跟一个异步执行的任务(callback)，那么这个时候，Tasks队列里，除了当前正在执行的script之外，会在后面压入一个setTimeout callback， 而这个callback的调用时机，就是在当前同步任务完成之后，才会调用。这就是为什么,’setTimeout’ 会出现在’script end’之后了。

  MicroTasks，这个和setTimeout不同，因为它是在当前Task完成后，就立即执行的，或者可以理解成，’microTasks总是在当前任务的最后执行’。 另外，还有一个非常重要的特性是： 如果当前JS stack如果为空的时候(比如我们绑定了click事件后，等待和监听click时间的时候，JS stack就是空的),一会立即执行。 关于这一点，之后有个例子会具体说明，先往下看。

  那么MicroTasks队列主要是promise和mutation observer 的回掉函数生成 

## Q1

```
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});

console.log('script end');
```

Results:

```
script start
script end
promise1
promise2
setTimeout
```
  *Chrome以外的浏览器会有不同的结果：Microsoft Edge, Firefox 40, iOS Safari and desktop Safari 8.0.8 log setTimeout before promise1 and promise2 - although it appears to be a race condition. This is really weird, as Firefox 39 and Safari 8.0.7 get it consistently right.

运行过程动画：

![Image text](https://user-gold-cdn.xitu.io/2018/8/5/1650827c4eb4d2a5?w=420&h=306&f=gif&s=441671)

## Q2

![Image text](https://user-gold-cdn.xitu.io/2018/8/5/1650914100613dfd?w=225&h=194&f=png&s=1245)

如上div元素，结构为
```
<div class="outer">
  <div class="inner"></div>
</div>
```

js代码如下,

1. 现在如果点击里面的方块，控制台会输出什么呢？

2. 直接执行inner.click()，控制台会输出什么呢？

```
// Let's get hold of those elements
var outer = document.querySelector('.outer');
var inner = document.querySelector('.inner');

// Let's listen for attribute changes on the
// outer element
new MutationObserver(function() {
  console.log('mutate');
}).observe(outer, {
  attributes: true
});

// Here's a click listener…
function onClick() {
  console.log('click');

  setTimeout(function() {
    console.log('timeout');
  }, 0);

  Promise.resolve().then(function() {
    console.log('promise');
  });

  outer.setAttribute('data-random', Math.random());
}

// …which we'll attach to both elements
inner.addEventListener('click', onClick);
outer.addEventListener('click', onClick);
```

Result No.1

```
click
promise
mutate
click
promise
mutate
timeout
timeout
```

Result No.2

```
click
click
promise
mutate
promise
timeout 
timeout
```



  *以上内容摘自https://forrany.github.io/2018/09/04/Tasks,-microtasks,-queues-and-schedules/
