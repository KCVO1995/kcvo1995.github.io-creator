---
title: "jQuery设计思想及其简单实现"
date: 2020-01-19T12:23:26+08:00
draft: false
---

### 前言

#### jQuery 是目前最长寿也是使用最广泛的 javaScript 函数库,据统计全球排名前 100k 的网站,有 90%都在使用 jQuery,远远超过了其他的库,数据如下图

![jQuery 使用数据](/images/jQuery.png)

#### jQuery 的实现都是基于 DOM api 的,大家都知道 js 提供的 dom 操作代码繁琐,而 jQuery 的核心里历练 wirte less,do more.

#### 当我们使用 jQuery 时,只需使用$($是 jQuery 的别名)就会声明一个全局函数,这个函数接受一个选择器,以获取对应的元素,之后便返回一个由 jQuery 构造出来对象,也称 jQuery 对象,此对象包含了可操作元素的方法.

### 一、jQuery 如何获取元素

jQuery 的基本设计思想和主要用法,就是'选择某个网页元素,然后对其进行某种操作'

jQuery 使用\$(selector) 的方法获取页面的操作元素,selector 接受字符串或着是元素组成的数组
我们可以使用 DOM API 的 querySelectorAll 进行类似的实现

```
window.$ = window.jQuery = function (selectorOrArray) {
let elements
if(typeof selectorOrArray === 'string'){
elements = document.querySelectorAll(selectorOrArray)
}else if(selectorOrArray instanceof Array){
elements = selectorOrArray
}
return {//返回一个可操作 elements 的对象}
}
```

#### 查找元素

当我们获取元素后,需要找到并操作其内部的元素时,可以调用 jQuery 对象里面的 find()函数
find()函数的实现,需要使用 for 循环来遍历,并将新生成的数组放入\$(selector)

```
find(selector){
        let array = []
        for(let i = 0; i < elements.length; i++){
            array.concat(Array.from(elements[i].querySelectorAll(selector)))
        }
        return $(array)
}
```

#### 遍历函数

在 jQuery 对象的操作方法中,遍历是经常用到的,那我们何不封装一个遍历函数 each() 来调用呢?

```
each(fn){
for(let i = 0; i < elements.length; i++){
fn.call(null, elements, i)
}
return this
}
```

如此一来,有了 each()函数,find() 函数就能更简洁的实现了,如下

```
find(selector){
 let array = []
this.each(node => {
array.concat(Array.from(node.querySelectorAll(selector)))
})
return \$(array)
}
```

### 二、jQuery 的链式操作是怎样的

jQuery 设计思想之一,获取元素后,可以对它进行一系列的操作,并且所有操作可以连接在一起,称为链式操作,举个栗子:

`$('div').find('h3').eq(2).html('Hello')`

分解开来，就是下面这样

```
\$('div') //找到 div 元素

.find('h3') //选择其中的 h3 元素

.eq(2) //选择第 3 个 h3 元素

.html('Hello'); //将它的内容改为 Hello
```

从上面简易实现的代码可以看出，jQuery 的操作函数运行后，都会返回一个 jQuery 对象，这样是的不同操作可以连在一起，这是 jQuery 最令人称道、最方便的特点

### 三、jQuery 如何创建元素与属性的处理

- 创建元素节点

  `$("<div></div>")`

- 创建为文本节点

  `$("<div>我是文本节点</div>")`

- 创建为属性节点

  `$("<div class='right'><div class='aaron'>动态创建 DIV 元素节点</div></div>")`

这就是 jQuery 创建节点的方式，让我们保留 HTML 的结构书写方式，非常的简单、方便和灵活

### 四、jQuery 如何移动元素

jQuery 设计思想之一，就是提供两组方法，来操作元素在网页中的位置移动。一组方法是直接移动该元素，另一组方法是移动其他元素，使得目标元素达到我们想要的位置。

- 第一种方法是使用.insertAfter()，把 div 元素移动 p 元素后面：

  `$('div').insertAfter($('p'))`

- 第二种方法是使用.after()，把 p 元素加到 div 元素前面：

  `$('p').after($('div'))`

表面上看，这两种方法的效果是一样的，唯一的不同似乎只是操作视角的不同。但是实际上，它们有一个重大差别，那就是返回的元素不一样。第一种方法返回 div 元素，第二种方法返回 p 元素。你可以根据需要，选择到底使用哪一种方法。

#### 使用这种模式的操作方法，一共有四对：

- .insertAfter()`和.after()：在现存元素的外部，从后面插入元素
- .insertBefore()和.before()：在现存元素的外部，从前面插入元素
- .appendTo()和.append()：在现存元素的内部，从后面插入元素
- .prependTo()和.prepend()：在现存元素的内部，从前面插入元素

### 五、jQuery 如何修改元素的属性

jQuery 提供多种修改元素的属性的方法：

- attr()函数获取标签的某个属性

  `$('#p').attr('class') //获取id为p的标签的属性值`

- attr()修改或修改标签的属性值

  `$('#p').attr('class','red') //如果存在就是修改，如果不存在就是添加`

- 删除某个属性

  `$('#p').removeAttr('name')`

- 添加 class 名称

  `$('#p').addClass('blue')`

- 删除 class 名称

  `$('#p').removeClass('yellow')`

- 有 class 就删除 没有就添加

  `$('#p').toggleClass('asdf')`

- val()获取输入框内容

  `var s = \$('input[name="user"]').val()`

参考资料：jQuery 设计实现 - 阮一峰的网络日志
