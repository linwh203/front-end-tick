---
title: Coding Questions
---

Question: What is the value of `foo`?
```javascript
var foo = 10 + '20';
```
Answer: "1020"

Question: What will be the output of the code below?
```javascript
console.log(0.1 + 0.2 == 0.3);
```
Answer: false => 0.1 + 0.2 = 0.300000000000001 => 0.2 + 0.4 = 0.60000000001 ... and so

Question: How would you make this work?
```javascript
add(2, 5); // 7
add(2)(5); // 7
```
Best Answer:
```javascript
const add = (...args)=> {
    let sum = args.reduce((pre,crt)=> pre + crt);
    const accu = (...args2)=> {
        sum += args2.reduce((pre,crt)=> pre+crt);
        return accu;
    }
    accu.valueOf = ()=> sum;
    return accu;
}
```

Question: What value is returned from the following statement?
```javascript
"i'm a lasagna hog".split("").reverse().join("");
```
Answer: "goh angasal a m'i"

Question: What is the value of `window.foo`?
```javascript
( window.foo || ( window.foo = "bar" ) );
```
Answer:  "bar"

Question: What is the outcome of the two alerts below?
```javascript
var foo = "Hello";
(function() {
  var bar = " World";
  alert(foo + bar);
})();
alert(foo + bar);
```
Answer: first alert => Hello World; second alert didn't trigger, throw error => bar is not defined

Question: What is the value of `foo.length`?
```javascript
var foo = [];
foo.push(1);
foo.push(2);
```
Answer: 2

Question: What is the value of `foo.x`?
```javascript
var foo = {n: 1};
var bar = foo;
foo.x = foo = {n: 2};
```
Answer: undefined => but bar.x is { n: 2} 
img src="https://img-blog.csdn.net/20161128211727305?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"

Question: What does the following code print?
```javascript
console.log('one');
setTimeout(function() {
  console.log('two');
}, 0);
Promise.resolve().then(function() {
  console.log('three');
})
console.log('four');
```
Answer: one => four => three => two

Question: What is the difference between these four promises?
```javascript
doSomething().then(function () {
  return doSomethingElse();
});

doSomething().then(function () {
  doSomethingElse();
});

doSomething().then(doSomethingElse());

doSomething().then(doSomethingElse);
```
