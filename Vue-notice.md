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







