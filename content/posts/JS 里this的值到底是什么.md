---
title: "JS 里this的值到底是什么？"
date: 2020-02-13T15:12:26+08:00
draft: false
---

### 说到this，那就是面试的必考的题，如果你还没搞懂this，就请看看这篇文章吧。

面试中你可能会遇到这样的题目

```
let a = {
  speak: function() {
    console.log(this)
  }
}

let b = a.speak
b()           // 打印出的this 是 window
a.speak()     // 打印出的this 是 a
```

#### 最后两行运行的结果怎么不一样了？

#### 看起来很迷对吧，看完下文，你还会这么想吗？

## 函数调用

弄懂函数调用是学习this的关键

我们先来看看JS（ES5） 的三种函数调用形式

```
fn(p1, p2)
obj.fn(p1, p2)
fn.call(p0, p1, p2)
```

大多数的初学者都习惯使用前两种调用方式，然而这样并不利于我们了解this

实际上第三种调用方法才是正确的调用方式，也就是

```
fn.call(p0, p1, p2)
```

其他两种调用方式都是语法糖，并且可以等价的改为 call 形式

```
fn(p1, p2)  // 等价于
fn.call(undefined, p1, p2)

obj.fn(p1, p2)  // 等价于
fn.call(obj, p1, p2)
```

#### 为了方便下文描述，我们把这样的代码为 ' 转换代码 '

这样，this 就很好解释了

#### 使用fn.call(p0, p1, p2)方式调用，参数p0传入的值就是this。

先来看看 fn(p1, p2) 中的this如何确定

```
function fn () {
  console.log(this)
}
fn()
// 我们用 ' 转换代码 ', 转化一下

function fn () {
  console.log(this)
}
fn.call(undefined) // 可以简写成 fn.call()
```

那按理来说打印出的应该就是undefined 了吧，但是浏览器里面有一条规则：

如果call 传入的p0 是undefined 或者是 null 的话，那么 window 对象就是默认的 p0 （在严格模式下， undefined 是默认的p0）

如此一来上面的答案显而易见了，就是打印出window

如果你不希望this是window对象，很简单

改变p0的传入值

`fn.call(obj)  // 好了，现在this就是obj了 `

现在，再看看第一条题目

```
let a = {
  speak: function() {
    console.log(this)
  }
}

let b = a.speak
b()           
a.speak()     
使用转换代码，转化

b.call(undefined)   // this 就是 window 
a.speak.call(a)     // this 就是 a
```


## [ ] 语法

```
function fn() { console.log(this) }
let arr = [fn, fn2]
arr[0]() // 这里面的this 又是什么？
```

#### 我们可以吧 arr[0]() 想象为 arr.0() ，尽管这样的语法是错的，但是形式与转换代码里的obj.child.method(p1, p2)对应上了，于是我们就可以愉快的转换啦

arr[0]

假想为 arr.0()

再转换为 arr.0.call( arr )

答案出来了 this 就是 arr


## Event Handler 中的 this

```
btn.addEventListener('click', function handler() {
  console.log(this)  // 请问这里的this是什么
})
```

按照上文所说，只要转换成call 形式，就能知道答案了

那只要找到 handler 被调用时的代码就可以了，可是源代码呢？

由于 addEventListener 是浏览器提供的内置方法，源代码我们也看不了，那我们只能看文档了

#### 通常来说this的值是触发事件的元素的引用，这种特性在多个相似的元素使用同一个通用事件监听器时非常让人满意。

#### 当使用 addEventListener() 为一个元素注册事件的时候，句柄里的 this 值是该元素的引用。其与传递给句柄的 event 参数的 currentTarget 属性的值一样。

按照文档的解释，浏览器的源代码，你可以假象为是这样写的

```
// 当事件触发时
handler.call( event.currentTarget, event )
// 答案不言而喻了
```

## jQuery Event Handler 中的 this

```
$ul.on('clikc', 'li', function() {
  console.log(this)
})
```

同样，不需要瞎猜，我们看看jQuery文档，试试能不能找到答案

#### 当jQuery的调用处理程序时，this关键字指向的是当前正在执行事件的元素。对于直接事件而言，this代表绑定事件的元素。对于代理事件而言，this则代表了与selector相匹配的元素。(注意，如果事件是从后代元素冒泡上来的话，那么this就有可能不等于event.target。)若要使用 jQuery 的相关方法，可以根据当前元素创建一个 jQuery 对象，即使用$(this)。




## 箭头函数

箭头函数是es6的新语法，它和es5 的函数最大的区别就是，箭头函数里面没有this 和 arguments ，所以当你在箭头函数里面见到this，你直接把它当作箭头函数外面的this即可。

因为箭头函数不支持this，也不能改变this，所有外面的this是什么，箭头函数里面的this还是什么。



## 总结

* this 就是你call 一个函数时，传入的第一个参数
* 如果你使用的函数调用方式不是call形式，就按照 ' 转换代码 ' 转化为call 形式
* 使用已经封装好的函数要确认this的时候，就去看看源码或者是文档吧。
