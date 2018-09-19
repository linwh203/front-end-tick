# 函数节流和函数防抖

## * 函数防抖和函数节流都是防止某一时间频繁触发，但是这两兄弟之间的原理却不一样。
## * 函数防抖是某一段时间内只执行一次，而函数节流是间隔时间执行。

## 应用场景：

### debounce

  * search搜索联想，用户在不断输入值时，用防抖来节约请求资源。
  * window触发resize的时候，不断的调整浏览器窗口大小会不断的触发这个事件，用防抖来让其只触发一次

### throttle

  * 鼠标不断点击触发，mousedown(单位时间内只触发一次)
  * 监听滚动事件，比如是否滑到底部自动加载更多，用throttle来判断

## 1.函数防抖(debounce)

### 原理：在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。

```
//模拟一段ajax请求
function ajax(content) {
  console.log('ajax request ' + content)
}

function debounce(fun, delay) {
    return function (args) {
        let that = this
        let _args = args
        clearTimeout(fun.id)
        fun.id = setTimeout(function () {
            fun.call(that, _args)
        }, delay)
    }
}
    
let inputb = document.getElementById('debounce')

let debounceAjax = debounce(ajax, 500)

inputb.addEventListener('keyup', function (e) {
        debounceAjax(e.target.value)
    })

```

  使用场景：当用户在input频繁的输入时，并不会发送请求，只有在指定间隔内没有输入时，才会执行函数。如果停止输入但是在指定间隔内又输入，会重新触发计时。 

## 函数节流(throttle)

### 原理：规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。

```
function throttle(fun, delay) {
        let last
        return function (args) {
            let that = this
            let _args = arguments
            let now = +new Date()
            if (!last || now >= last + delay) {
                last = now
                fun.apply(that,_args)
            }
        }
}

let throttleAjax = throttle(ajax, 1000)

let inputc = document.getElementById('throttle')
inputc.addEventListener('keyup', function(e) {
    throttleAjax(e.target.value)
})
```

  不管我们设定的执行时间间隔多小，总是1s内只执行一次。



  *以上摘自薄荷前端
