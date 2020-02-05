---
title: "JS 的 new 到底是干什么的？"
date: 2020-01-02T15:23:26+08:00
draft: false
---


大部分讲new的文章都会从面向对象的思路来讲，但是对于新手学习是，引入一个更复杂的概念，无疑是增加了学习了难度。

本文将从[省代码]的角度讲解new

举一个简单的栗子，有一条制造iPhoneX的流水线，生产出的iPhoneX在计算机里就是一堆属性，如下图：

![iPhoneX](/images/iPhoneX.png)

写成代码如下

```
let iPhoneX = {
    ID: 1,
    品牌: '苹果',
    颜色: '土豪金', //有多种配色
    屏幕: '5.8英寸',
    开机: function(){/* 开机代码 */},
    死机: function(){/* 死机代码 */}
}
```

#### 制作1000台iPhoneX

```
let iPhoneXs = []
let iPhoneX 
for (let i = 0; i < 1000; i++) {
    iPhoneX = {
    ID: i,  //ID号不能重复
    品牌: '苹果',
    颜色: '土豪金',  //有多种配色
    屏幕: '5.8英寸',
    开机: function(){/* 开机代码 */},
    死机: function(){/* 死机代码 */}
    }
} 
```
OK弄好了，so easy 

#### 质疑
上面的代码存在一个问题：浪费了很多内存。

开机代码和死机代码对于每台iPhoneX其实是一样的，只需要各自引用同一个函数就可以了，没必要重复创建到每一个iPhoneX对象里

每台iPhoneX的品牌和屏幕尺寸也是相同的，同样没必要重复创建。

只有 ID 和 颜色 需要重复创建。

#### 改进

属性JS原型链的同学肯定知道，用原型链可以解决重复创建的问题，

``` 
let iPhoneXs = []
let iPhoneX 
let iPhoneX原型 = {
    品牌: '苹果',
    屏幕: '5.8英寸',
    开机: function(){/* 开机代码 */},
    死机: function(){/* 死机代码 */}
}
for (let i = 0; i < 1000; i++) {
    iPhoneX = {
    ID: i,  //ID号不能重复
    颜色: '土豪金' 
    }
    iPhoneX.__proto__ = iPhoneX原型
    iPhoneXs.push(iPhoneX)
}
```

#### 还不够优雅？

有人指出制造iPhoneX的代码分散在两个地方不够优雅，于是我们用一个函数把这两个部分连接起来：

``` 
let iPhoneXs = []
function iPhoneXCreate(ID) {                            //构造函数
    let iPhoneX = Object.create(iPhoneXCreate.iPhoneX原型)
    iPhoneX.ID = ID
    return iPhoneX
}
iPhoneXCreate.iPhoneX原型 = {
    品牌: '苹果',
    屏幕: '5.8英寸',
    开机: function(){/* 开机代码 */},
    死机: function(){/* 死机代码 */},X
    constructor: iPhoneXCreate
}
for (let i = 0; i < 1000; i++) {
    iPhoneXs[i] = iPhoneXCreate(i)
}
```

#### JS 之父的关怀

这一次我们用new来写

```
let iPhoneXs = []
function IPhoneX(ID) {
    this.ID = ID
}

IPhoneX.prototype.品牌 = '苹果'
IPhoneX.prototype.屏幕 = '5.8英寸'
IPhoneX.prototype.开机 = function(){/* 开机代码 */}
IPhoneX.prototype.死机 = function(){/* 死机代码 */}

for (let i = 0; i < 1000; i++) {
    iPhoneXs[i] = new IPhoneX(i)
}
```
完美，使用new省几行代码。（也就是所谓的语法糖）

只要你在IPhoneX前面使用 new 关键字，那么可以少做四件事情：

* 不用创建临时对象，因为 new 会帮你做（你使用「this」就可以访问到临时对象）；
* 不用绑定原型，因为 new 会帮你做（new 为了知道原型在哪，所以指定原型的名字为 prototype）；
* 不用 return 临时对象，因为 new 会帮你做；
* 不要给原型想名字了，因为 new 指定名字为 prototype。

本文参考方应杭的文章[JS 的 new 到底是干什么的？](https://zhuanlan.zhihu.com/p/23987456)