# 如何用各种方法代替for循环

## 一，用好 filter，map，和其它 ES6 新增的高阶遍历函数

### 问题一：将数组中的 false值去除
```
const arrContainsEmptyVal = [3, 4, 5, 2, 3, undefined, null, 0, ""];
```
### 答案：
```
const compact = arr => arr.filter(Boolean); 
```
### 问题二： 将数组中的 VIP 用户余额加 10 
```
const users = [   
  { username: "Kelly", isVIP: true, balance: 20 },   
  { username: "Tom", isVIP: false, balance: 19 },   
  { username: "Stephanie", isVIP: true, balance: 30 } 
];
```
### 答案： 
```
users.map(user =>(
    user.isVIP ? 
        {...user, balance: user.balance +10}
        : user
    )
);
```

补充：经网友提醒，这个答案存在浅拷贝的问题。操作引用型数据确实是一个麻烦的问题。下面提供两个方案：  
1. 用 Ramda：
```
import R from "ramda";

const add10IfVIP = R.ifElse(
    R.propEq("isVIP", true),
    R.evolve({ balance: R.add(10) }),
    R.identity
);

const updateUsers = R.map(add10IfVIP);
updateUsers(users);
```
2. 用 Immer如果你习惯写 mutable 的代码，可以试下 Immer，用 mutable 的风格写 immutable 的代码。
```
import produce from "immer";

const updatedUsers = produce(users, nextState => {
    nextState.forEach(user => {
    if (user.isVIP) {
        user.balance += 10;
        }
    });
});
```
### 问题三：判断字符串中是否含有元音字母
```
const randomStr = "hdjrwqpi";
```
### 答案：
```
const isVowel = char => ["a", "e", "o", "i", "u"].includes(char);
const containsVowel = str => [...str].some(isVowel);

containsVowel(randomStr);
```
问题四：判断用户是否全部是成年人
```
const users = [
  { name: "Jim", age: 23 },
  { name: "Lily", age: 17 },
  { name: "Will", age: 25 }
];
```
### 答案：
```
users.every(user => user.age >= 18);
```
### 问题五： 找出上面用户中的第一个未成年人答案：
```
const findTeen = users => users.find(user => user.age < 18);

findTeen(users);
```
### 问题六：将数组中重复项清除
```
const dupArr = [1, 2, 3, 3, 3, 3, 6, 7];
```
### 答案：
```
const uniq = arr => [...new Set(arr)];

uniq(dupArr);
```
### 问题七： 生成由随机整数组成的数组，数组长度和元素大小可自定义
### 答案：
```
const genNumArr = (length, limit) =>
  Array.from({ length }, _ => Math.floor(Math.random() * limit));

genNumArr(10, 100);
```
## 二，理解和熟练使用 reduce
### 问题八： 不借助原生高阶函数，定义 reduce
### 答案：
```
const reduce = (f, acc, arr) => {
  if (arr.length === 0) return acc;
  const [head, ...tail] = arr;
  return reduce(f, f(head, acc), tail);
};
```
### 问题九：将多层数组转换成一层数组
```
const nestedArr = [1, 2, [3, 4, [5, 6]]];
```
### 答案：
```
const flatten = arr =>
  arr.reduce(
    (flat, next) => flat.concat(Array.isArray(next) ? flatten(next) : next),
    []
  );
```
### 问题十：将下面数组转成对象，key/value 对应里层数组的两个值
```
const objLikeArr = [["name", "Jim"], ["age", 18], ["single", true]];
```
### 答案：
```
const fromPairs = pairs =>
  pairs.reduce((res, pair) => ((res[pair[0]] = pair[1]), res), {});

fromPairs(objLikeArr);
```
### 问题十一：取出对象中的深层属性
```
const deepAttr = { a: { b: { c: 15 } } };
```
### 答案：
```
const pluckDeep = path => obj =>
  path.split(".").reduce((val, attr) => val[attr], obj);

pluckDeep("a.b.c")(deepAttr);
```
### 问题十二：将用户中的男性和女性分别放到不同的数组里：
```
const users = [
  { name: "Adam", age: 30, sex: "male" },
  { name: "Helen", age: 27, sex: "female" },
  { name: "Amy", age: 25, sex: "female" },
  { name: "Anthony", age: 23, sex: "male" },
];
```
### 答案：
```
const partition = (arr, isValid) =>
  arr.reduce(
    ([pass, fail], elem) =>
      isValid(elem) ? [[...pass, elem], fail] : [pass, [...fail, elem]],
    [[], []],
  );
  
const isMale = person => person.sex === "male";

const [maleUser, femaleUser] = partition(users, isMale);
```
### 问题十三： reduce 的计算过程，在范畴论里面叫 catamorphism，即一种连接的变形。和它相反的变形叫 anamorphism。现在我们定义一个和 reduce 计算过程相反的函数 unfold（注：reduce 在 Haskell 里面叫 fold，对应 unfold）
```
const unfold = (f, seed) => {
  const go = (f, seed, acc) => {
    const res = f(seed);
    return res ? go(f, res[1], acc.concat(res[0])) : acc;
  };
  return go(f, seed, []);
};
```
根据这个 unfold 函数，定义一个 Python 里面的 range 函数。
### 答案：
```
const range = (min, max, step = 1) =>
  unfold(x => x < max && [x, x + step], min);
```
## 三，用递归代替循环（可以break！）
### Edit: 虽然递归爆栈的问题可以用代码解决，但递归确实性能赶不上循环。这部分内容纯粹当做递归函数案例了。如何解决递归爆栈，可以参考我的另一篇文章不懂递归？(https://juejin.im/post/5b5bce21f265da0f6b7710fb)
### 问题十四： 将两个数组每个元素一一对应相加。注意，第二个数组比第一个多出两个，不要把第二个数组遍历完。
```
const num1 = [3, 4, 5, 6, 7];
const num2 = [43, 23, 5, 67, 87, 3, 6];
```
### 答案：
```
const zipWith = f => xs => ys => {
  if (xs.length === 0 || ys.length === 0) return [];
  const [xHead, ...xTail] = xs;
  const [yHead, ...yTail] = ys;
  return [f(xHead)(yHead), ...zipWith(f)(xTail)(yTail)];
};

const add = x => y => x + y;

zipWith(add)(num1)(num2);
```
### 问题十五：将 Stark 家族成员提取出来。注意，目标数据在数组前面，使用 filter 方法遍历整个数组是浪费。
```
const houses = [
  "Eddard Stark",
  "Catelyn Stark",
  "Rickard Stark",
  "Brandon Stark",
  "Rob Stark",
  "Sansa Stark",
  "Arya Stark",
  "Bran Stark",
  "Rickon Stark",
  "Lyanna Stark",
  "Tywin Lannister",
  "Cersei Lannister",
  "Jaime Lannister",
  "Tyrion Lannister",
  "Joffrey Baratheon"
];
```
### 答案：
```
const takeWhile = f => ([head, ...tail]) =>
  f(head) ? [head, ...takeWhile(f)(tail)] : [];

const isStark = name => name.toLowerCase().includes("stark");

takeWhile(isStark)(houses);
```
### 问题十六：找出数组中的奇数，然后取出前4个：
```
const numList = [1, 3, 11, 4, 2, 5, 6, 7];
```
### 答案：
```
const takeFirst = (limit, f, arr) => {
  if (limit === 0 || arr.length === 0) return [];
  const [head, ...tail] = arr;
  return f(head)
    ? [head, ...takeFirst(limit - 1, f, tail)]
    : takeFirst(limit, f, tail);
};

const isOdd = n => n % 2 === 1;

takeFirst(4, isOdd, numList);
```
## 四，使用高阶函数遍历数组时可能遇到的陷阱
### 问题十七： 从长度为 100 万的随机整数组成的数组中取出偶数，再把所有数字乘以 3// 用我们刚刚定义的辅助函数来生成符合要求的数组
```
const bigArr = genNumArr(1e6, 100);
```
### 能运行的答案：
```
const isEven = num => num % 2 === 0;
const triple = num => num * 3;

bigArr.filter(isEven).map(triple);
```
注意，上面的解决方案将数组遍历了两次，无疑是浪费。如果写 for 循环，只用遍历一次：
```
const results = [];
for (let i = 0; i < bigArr.length; i++) {
  if (isEven(bigArr[i])) {
    results.push(triple(bigArr[i]));
  }
}
```
在我的电脑上测试，先 filter 再 map 的方法耗时 105.024 ms，而采用 for 循环的方法耗时仅 25.598 ms！那是否说明遇到此类情况必须用 for 循环解决呢? No！ 
## 五，死磕到底，Transduce！ 
我们先用 reduce 来定义 filter 和 map，至于为什么这样做等下再解释。
```
const filter = (f, arr) =>
  arr.reduce((acc, val) => (f(val) && acc.push(val), acc), []);

const map = (f, arr) => arr.reduce((acc, val) => (acc.push(f(val)), acc), []);
```
重新定义的 filter 和 map 有共有的逻辑。我们把这部分共有的逻辑叫做 reducer。有了共有的逻辑后，我们可以进一步地抽象，把 reducer 抽离出来，然后传入 filter 和 map：
```
const filter = f => reducer => (acc, value) => {
  if (f(value)) return reducer(acc, value);
  return acc;
};

const map = f => reducer => (acc, value) => reducer(acc, f(value));
```
现在 filter 和 map 的函数 signature 一样，我们就可以进行函数组合（function composition）了。
```
const pushReducer = (acc, value) => (acc.push(value), acc);

bigNum.reduce(map(triple)(filter(isEven)(pushReducer)), []);
```
但是这样嵌套写法易读性太差，很容易出错。我们可以写一个工具函数来辅助函数组合：
```
const pipe = (...fns) => (...args) => fns.reduce((fx, fy) => fy(fx), ...args);
```
然后我们就可以优雅地组合函数了：
```
bigNum.reduce(
  pipe(
    filter(isEven),
    map(triple)
  )(pushReducer),
  []
);
```
经过测试（用 console.time()/console.timeEnd()）,上面的写法耗时 33.898 ms，仅比 for 循环慢 8 ms。为了代码的易维护性和易读性，这点性能上的微小牺牲，我认为是可以接受的。 这种写法叫 transduce。有很多工具库提供了 transducer 函数。比如 transducers-js。除了用 transducer 来遍历数组，还能用它来遍历对象和其它数据集。功能相当强大。
## 六，for 循环和 for ... of 循环的区别 
### for ... of 循环是在 ES6 引入 Iterator 后，为了遍历 Iterable 数据类型才产生的。EcmaScript 的 Iterable 数据类型有数组，字符串，Set 和 Map。for ... of 循环属于重型的操作（具体细节我也没了解过），如果用 AirBNB 的 ESLint 规则，在代码中使用 for ... of 来遍历数组是会被禁止的。 那么，for ... of 循环应该在哪些场景使用呢？目前我发现的合理使用场景是遍历自定义的 Iterable。
### 问题十八： 将 Stark 家族成员名字遍历，每次遍历暂停一秒，然后将当前遍历的名字打印来，遍历完后回到第一个元素再重新开始，无限循环。
```
const starks = [
  "Eddard Stark",
  "Catelyn Stark",
  "Rickard Stark",
  "Brandon Stark",
  "Rob Stark",
  "Sansa Stark",
  "Arya Stark",
  "Bran Stark",
  "Rickon Stark",
  "Lyanna Stark"
];
```
### 答案：
```
function* repeatedArr(arr) {
  let i = 0;
  while (true) {
    yield arr[i++ % arr.length];
  }
}

const infiniteNameList = repeatedArr(starks);

const wait = ms =>
  new Promise(resolve => {
    setTimeout(() => {
      resolve();
    }, ms);
  });

(async () => {
  for (const name of infiniteNameList) {
    await wait(1000);
    console.log(name);
  }
})();
```
## 七，放弃倔强，实在需要用 for 循环了 
### 前面讲到的问题基本覆盖了大部分需要使用 for 循环的场景。那是否我们可以保证永远不用 for 循环呢？其实不是。我讲了这么多，其实是在鼓励大家不要写 for 循环，而不是不用 for 循环。我们常用的数组原型链上的 map，filter 等高阶函数，底层其实是用 for 循环实现的。在需要写一些底层代码的时候，还是需要写 for 循环的。
### 来看这个例子：
```
Number.prototype[Symbol.iterator] = function*() {
  for (let i = 0; i <= this; i++) {
    yield i;
  }
};

[...6]; // [0, 1, 2, 3, 4, 5, 6]
```
注意，这个例子只是为了好玩。生产环境中不要直接修改 JS 内置数据类型的原型链。原因是 V8 引擎有一个原型链快速推测机制，修改原型链会破坏这个机制，造成性能问题。
