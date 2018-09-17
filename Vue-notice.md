# 1.踩坑

## 1.1.深拷贝/浅拷贝

在js中也有栈（stack）和堆（heap）的概念

* 栈：自动分配的内存空间，大小确定会自动释放。存放变量/局部变量/形参等。在js中存放简单数据段（五种基本数据类型：Number、String、Boolean、Null、Undefined），他们是按值存放的，可以直接访问。
* 堆：动态分配的内存，大小不定并且不会自动释放。存放在堆内存中的对象，栈中的变量实际保存的是一个指针，这个指针指向堆中的某一个位置。

深拷贝的方法

* 方法一：逐个去拿到简单数据项（递归解决)

```
function deepClone(origin, target) {
  for (let key in origin) {
    if (origin.hasOwinProperty(key)) {
      if (Array.isArray(origin[key])) {
        target[key] = []
        deepClone(origin[key], target[key])
      } else if (typeof origin[key] === 'object' && origin[key]!== null) {
        target[key] = {}
        deepClone(origin[key], target[key])
      } else {
        origin[key] = target[key]
      }
    }
  }
  return target
}
```
* 方法二：通过JSON去解析

```
let copyObj = JSON.parse(JSON.stringify(obj));
```

* 方法三：es6之展开Object.assign

```
let copyObj = Object.assign({}, obj);
```

* 方法四：es6之展开运算符（仅用于数组）

```
let copyArr = [...obj];
```

## 1.2.列表更新检测

### 1.2.1数组更新检测

由于 JavaScript 的限制，Vue 不能检测以下变动的数组：

当你利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue
当你修改数组的长度时，例如：vm.items.length = newLength
为了解决第一类问题，以下两种方式都可以实现和 vm.items[indexOfItem] = newValue 相同的效果，同时也将触发状态更新：
```
// Vue.set
Vue.set(example1.items, indexOfItem, newValue)
// Array.prototype.splice
example1.items.splice(indexOfItem, 1, newValue)
```
为了解决第二类问题，你可以使用 splice：
```
example1.items.splice(newLength)
```
* 触发视图更新的方法：

push()、pop()、shift()、unshift()、splice()、sort()、reverse()

* vue提供的set方法

Vue.set( target, key, value )

### 1.2.2对象更新检测

由于 JavaScript 的限制，Vue 不能检测对象属性的添加或删除：

```
var vm = new Vue({
  data: {
    a: 1
  }
})   // `vm.a` 现在是响应式的

vm.b = 2   // `vm.b` 不是响应式的
```
解决方法：vue提供的set方法

Vue.set( target, key, value )

Object.assign()

bject.assign({}, target, {key:value})

## 1.3.页面刷新vuex被清空

* 这真的是遇到一个很坑的问题，同一个页面(router未改变)，一旦刷新（刷新或深度刷新），存储的vuex就马上和你说拜拜

* localStorage

网上推荐最多的方法就是用localStorage。但是我个人觉得不太合适，还得看项目吧。localStorage是永久存储的。

* 数据重新获取

我使用的方法是在需要某些数据之前先判断一下数据是否存在，如果不存在重新获取。

## 1.4.nextTick适当使用

将回调延迟到下次 DOM 更新循环之后执行。在修改数据之后立即使用它，然后等待 DOM 更新。它跟全局方法 Vue.nextTick 一样，不同的是回调的 this 自动绑定到调用它的实例上。

简而言之，等待DOM更新之后再进行操作。

* 什么时候需要用的Vue.nextTick()？

你在Vue生命周期的created()钩子函数进行的DOM操作一定要放在Vue.nextTick()的回调函数中。原因是什么呢，原因是在created()钩子函数执行的时候DOM 其实并未进行任何渲染，而此时进行DOM操作无异于徒劳，所以此处一定要将DOM操作的js代码放进Vue.nextTick()的回调函数中。与之对应的就是mounted钩子函数，因为该钩子函数执行时所有的DOM挂载和渲染都已完成，此时在该钩子函数中进行任何DOM操作都不会有问题 。

在数据变化后要执行的某个操作，而这个操作需要使用随数据改变而改变的DOM结构的时候，这个操作都应该放进Vue.nextTick()的回调函数中。

原因是，Vue是异步执行dom更新的，一旦观察到数据变化，Vue就会开启一个队列，然后把在同一个事件循环 (event loop) 当中观察到数据变化的 watcher 推送进这个队列。如果这个watcher被触发多次，只会被推送到队列一次。这种缓冲行为可以有效的去掉重复数据造成的不必要的计算和DOm操作。而在下一个事件循环时，Vue会清空队列，并进行必要的DOM更新。
当你设置 vm.someData = 'new value'，DOM 并不会马上更新，而是在异步队列被清除，也就是下一个事件循环开始时执行更新时才会进行必要的DOM更新。如果此时你想要根据更新的 DOM 状态去做某些事情，就会出现问题。。为了在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用 Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。

## 1.5.异步问题

这个是一个亘古不变的话题。

请求后台数据异步，常不经意的带来了问题。（处理异步的方法就不详细描述了，网上一搜一大堆）

## 1.6.组件之间的调用方式

### 1.6.1.父子组件

* prop向下传递，事件向上传递

* 子组件添加ref属性，父组件可以获取到子组件的实例（不建议）

* 插槽slot 作用域插槽

### 1.6.2.非父子组件

使用状态管理，实例化一个公共vue实例

### 1.7.计算属性设置值

计算属性是基于它们的依赖进行缓存的，一旦依赖发生变化，计算属性会重新计算

想要改变计算属性的值。要通过set方法去触发它所依赖的变量,(类似于触发它重新计算，单纯赋予一个新值，在取的时候也是不会被改变的)

### 1.8.vue文件中内联样式中有无scoped属性的差别

* 有scoped属性:当前仅当该vue文件可以使用这个样式。

* 无scoped属性：影响其他文件

### 1.9 v-for v-key

当 Vue.js 用v-for 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。

为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有的且唯一的 id。这个特殊的属性相当于 Vue 1.x 的 track-by ，但它的工作方式类似于一个属性，所以你需要用 v-bind 来绑定动态值 (在这里使用简写)：

### 1.10 v-for 和 v-if

当它们处于同一节点，v-for的优先级比v-if更高。

### 1.11 js文件中引入的css不会自动加前缀

无论是开发环境还是生成环境都不会自动加前缀，因为vue-loader只管.vue文件里面的样式，没有自动执行autoprefixer loader

* 在build/utils.js下引入postcss-loader
```
var postcssLoader = {
    loader: 'postcss-loader',
    options: {
        plugins: (loader) => [
            require('autoprefixer')()
        ],
        sourceMap: true
    }
}
```
如果还有问题在改成
```
var postcssLoader = {
    loader: 'postcss-loader',
    options: {
        plugins: (loader) => [
            require('autoprefixer')({
                browsers: [
                    // 加这个后可以出现额外的兼容性前缀
                    "> 0.01%"
                ]
            })
        ],
        sourceMap: true
    }
}
```

### 1.12 组件、prop大小写不敏感，事件敏感

跟组件和 prop 不同，事件名不会被用作一个 JavaScript 变量名或属性名，所以就没有理由使用 camelCase 或 PascalCase 了。并且 v-on 事件监听器在 DOM 模板中会被自动转换为全小写 (因为 HTML 是大小写不敏感的)，所以 v-on:myEvent 将会变成 v-on:myevent——导致 myEvent 不可能被监听到。

因此，我们推荐你始终使用 kebab-case 的事件名。

### 1.13 refs是静态节点

refs是静态节点

### 1.14 prop值的改变--不是立即

如果父组件中给子组件传递了一个prop的值，然后调用子组件的方法去获取该值，会发现值没有立即改变。

解决方法：

1. 可以监听值的改变去调用相应子组件的方法

2. 将子组件相关方法的调用放在nextTick里面

### 1.15 elmentui里面的el-form '就地复用'

和v-for一样，更新已渲染过的元素时，它默认用‘就地复用’策略。如果数据项的顺序被改变，Vue将不会移动DOM元素来匹配数据项的顺序，而是简单服用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。

### 1.16 elementui中的el-table的列显示与隐藏

如果有一组按钮组，点击不同的tab的时候显示不同的列，列会更换位置。默认用‘就地复用’，不会更新，需要加一个key值取随机数

# 2.优化
## 2.1.错误处理

### 2.1.1.请求接口错误
由于我的请求是使用axios插件或者fetch单独写在了一个js，可以对其进行响应拦截。一旦失败，或者后台报错，就进行响应的错误处理以及友好提示，也避免了重复的代码，提高可维护性

### 2.1.2.页面错误处理（404）
nginx未匹配到路由走404路由

router.beforeEach是否匹配到响应的路由，否则走错误路由。

### 2.1.3 错误提示封装
将错误提示模块化，通过vuex来操作错误的显示以及信息等内容。

## 2.2.减少不必要的依赖包
性能优化是很重要的，特别是对于vue这种首屏加载时间长的。

例如有些项目用到了图表（echarts）,可以选择加载依赖包，不用加载整个echarts库。

## 2.3.不发送多个相同的请求
不发送多个相同的请求，在点击触发请求的同时锁定请求，直至给出响应/错误解锁。

## 2.4.组件中引入css的css依赖 -- sass-resources-loader
为了让SCSS之类的文件在CSS中引入中不需要每次都引入var.scss文件，可以引入一个sass-resources-loader解决。

## 2.5.为了解决组件内引入的外部css文件没有做css兼容处理 -- postcss-loader

在build/utils中引入postcss-loader
```
loader: 'postcss-loader',
options:{
  plugins: (loader) => [
      require('autoprefixer')()
  ],
  sourceMap: true 
}
```

### *以上大部分转自 FIONA-SUN的博客















