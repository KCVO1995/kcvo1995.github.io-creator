---
title: "JavaScript对象基本用法"
date: 2019-12-31T15:15:26+08:00
draft: false
---

## 对象是什么？

对象（object）是 JavaScript 语言的核心概念，也是最重要的数据类型。

什么是对象？简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。

## 声明对象的两种语法

* let obj = { 'name' : 'Jacky', 'age' : 18 }
* let obj = new Object{ 'name' : 'Jacky', 'age' : 18 }

以上两种方法都可以声明对象，第二种写法更规范，第一种是简化写法。

细节：
* 键名是字符串，可以包含任何字符
* 键名引号可以省略，但省略以后只能写标识符
* 就算引号省略了，键名还是会转成字符串保存

#### 如何用变量做属性名

``` 
let a = 'name'
let obj = { [a] : 'Jacky' }
```
使用中括号可以用变量做键名，如果是{ a : 'jacky'},这样键名将会是'a'。

## 如何删除对象的属性

* delete.obj.xxx 或者 delete.obj['xxx']

delete命令删除对象的属性。删除成功后，就会返回true。

要注意如果删除一个不存在的属性，也会返回true，只有在删除不能删除的属性时才会返回false。

#### 如何查看删除是否成功

* 'xxx' in obj === false

'xxx'不是obj的属性

* 'xxx' in obj && obj.xxx === undefined

'xxx'是obj的属性，值为undefined

## 如何查看对象的属性

* Object.keys(obj)

查看自身的所有属性

* console.dir(obj)

查看自身属性和共有属性

* obj.hasOwnProperty('toString')

判断一个属性是自身的还是共有的

## 如何修改或增加对象的属性

#### 修改对象属性

* obj['name'] = 'jacky'

修改自身属性

* obj.name = 'jacky'

修改自身属性

* Object.assign(obj, {'age' : 18 ...})

批量修改自身属性

* Object.prototype['toString'] = 'xxx'

修改共有属性，但不建议这样做

* let obj = Object.create(common)
  
把obj的共有属性修改为common

#### 增加对象属性

增加对象属性的方法同上，已有属性则改，没有属性则增

## 区分obj[name]和obj['name']和obj.name

这三个表达式看起来都一样，让人傻傻分不清

但实际上，obj['name'] === obj.name ，name都是字符串

obj[name],name则是变量

不服请看下图

![区分](/images/对象-区分.png)