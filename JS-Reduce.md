# 数组reduce方法详解

从最简单的例子开始。
```
var arr = [1, 2, 3, 4, 5];
var sum = arr.reduce(function(prev, cur, index, arr) {
    console.log(prev, cur, index);
    return prev + cur;
})
console.log(arr, sum);
```
输出结果
```
1 2 1
3 3 2
6 4 3
10 5 4
[1, 2, 3, 4, 5] 15
```
回顾一下reduce中回调函数的参数，这个回调函数中有4个参数，意思分别为

prev: 第一项的值或者上一次叠加的结果值

cur: 当前会参与叠加的项

index： 当前值的索引

arr: 数组本身

首先我们要区分prev与cur这2个参数的区别。

prev表示每次叠加之后的结果，类型可能与数组中的每一项不同，而cur则表示数组中参与叠加的当前项。

上例中其实值遍历了4次，数组有五项。数组中的第一项被当做了prev的初始值，而遍历从第二项开始。

看下面一个例子。

某同学的期末成绩如下表示
```
var result = [
    {
        subject: 'math',
        score: 88
    },
    {
        subject: 'chinese',
        score: 95
    },
    {
        subject: 'english',
        score: 80
    }
];
```
如何求该同学的总成绩？

很显然，利用for循环可以很简单得出结论
```
var sum = 0;
for(var i=0; i<result.length; i++) {
    sum += result[i].score;
}
```
如何使用reduce来搞定这个问题
```
var sum = result.reduce(function(prev, cur) {
    return cur.score + prev;
}, 0);
```
给reduce参数添加了第二个参数,通过打印发现设置了这个参数之后，reduce遍历便已经从第一项开始了。

这第二个参数就是设置prev的初始类型和初始值，比如为0，就表示prev的初始值为number类型，值为0，因此，reduce的最终结果也会是number类型。

因为第二个参数为累计结果的初始值，因此假设该同学因为违纪被处罚在总成绩总扣10分，只需要将初始值设置为-10即可。
```
var sum = result.reduce(function(prev, cur) {
    return cur.score + prev;
}, -10);
```
假如该同学的总成绩中，各科所占的比重不同，分别为50%，30%，20%，如何求出最终的权重结果呢？

解决方案如下：
```
var dis = {
    math: 0.5,
    chinese: 0.3,
    english: 0.2
}
var sum = result.reduce(function(prev, cur) {
    console.log(prev);
    return cur.score + prev;
}, -10);
var qsum = result.reduce(function(prev, cur) {
    return prev + cur.score * dis[cur.subject]
}, 0)
console.log(sum, qsum);
```
为了计算出权重之后的总值，在回调函数内部修改了数组当前项，使他和权重比例关联，并重新返回一个一样的回调函数，将新修改的当前项传入，就和之前的例子是一样的了。

如何知道一串字符串中每个字母出现的次数？运用reduce来解决这个问题。

在reduce的第二个参数里面初始了回调函数第一个参数的类型和值，将字符串转化为数组，那么迭代的结果将是一个对象，对象的每一项key值就是字符串的字母。
```
var arrString = 'abcdaabc';
arrString.split('').reduce(function(res, cur) {
    res[cur] ? res[cur] ++ : res[cur] = 1
    return res;
}, {})
```
由于可以通过第二参数设置叠加结果的类型初始值，因此这个时候reduce就不再仅仅只是做一个加法了，可以灵活的运用它来进行各种各样的类型转换，比如将数组按照一定规则转换为对象，也可以将一种形式的数组转换为另一种形式的数组。
```
[1, 2].reduce(function(res, cur) { 
    res.push(cur + 1); 
    return res; 
}, [])
```
这种特性使得reduce在实际开发中大有可为！但是需要注意点，在ie9一下的浏览器中，并不支持该方法 ！
