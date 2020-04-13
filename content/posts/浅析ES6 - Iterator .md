---
title: "浅析 ES6 - Iterator"
date: 2020-02-13T15:12:26+08:00
draft: false
---

JavaScript 中表示“集合”的数据结构有很多，包括有字符串、数组、map、set.... ,那么对于不同的数据结构，我们会怎样遍历呢？

- 数组：`forEach` 、`for 循环`
- 字符串：`for in` 、`for 循环`
- map 对象：`forEach`

看起来每种数据结构都有遍历的方法，实现方法有很多，`for 循环` 、`forEach` 、`for in`

但是有没有发现一个问题，站在一个更高的维度去看待，其实这些方法都不能通用，也就是说上面这些数据集合，都不能用统一的遍历方法进行数据获取

那有没有一种更好的，通用方法，一个解决所有呢？

在 ES5 的时候还没有，道理 ES6 这个更好的方法就有了

## for of 循环，一个遍历所有数据的统一方法

引用阮一峰的话

### ES6 借鉴 C++、Java、C# 和 Python 语言，引入了 for...of 循环，作为遍历所有数据结构的统一的方法。关键在于统一，另一个就是直接取值，简化操作，不需要在声明和维护什么变量，对数据做转换

原来 `for of` 这么厉害，可以为不同的数据结构提供一种统一的数据获取方式

然而 `for of` 真的这么强大吗？`for of` 只是一种 ES6 的新语法而已

`for of` 并不是能遍历所有的对象，只有实现了 `Iterator` 接口的对象才能够使用 `for of` 来进行遍历

所以说 `for of` 真正强大的背后，是依靠 `Iterator`

## 真正的主角 - Iterator 迭代器

`Iterator` 是一种接口，目的是为不同的数据结构提供统一的数据访问机制，任何数据结构只要部署了 `Iterator` 接口，就可以实现遍历操作

但是由于 JavaScript 语言中没有接口的概念，我们可以把它理解成一种特殊的对象 - **迭代器对象**，返回这个对象的方法叫做**迭代器方法**

一个迭代器对象具有一个 `next` 方法，每次调用 `next` 方法都会返回一个结果

这个结果是一个 Object ,它包含两个属性，`value` 和 `done`

- `value` - 表示具体的返回值
- `done` - 是一个布尔值，表示集合是否遍历完成或者是否后续还有可用的数据， 没有可用的数据则返回 `true` ,否则返回 `false`
  另外内部会维护一个指针，每一次调用 `next` 方法，会返回数据结构当前的成员的信息，就是返回一个包含 `value` 和 `done` 两个属性的对象， 然会指针会向后移动一个位置，周而复始

描述了这么多，我们来看看代码是怎样实现的把

```javascript
function getIterator(list) {
  let nextIndex = 0;
  return {
    next: function () {
      return nextIndex < list.length
        ? { value: list[nextindex++], done: false }
        : { value: undefined, done: true };
    },
  };
}
const it = getIterator(["a", "b", "c"]);
console.log(it.next()); // {value: "a", done: false}
console.log(it.next()); // {value: "b", done: false}
console.log(it.next()); // {value: "c", done: false}
console.log(it.next()); // "{ value: undefined, done: true }"
console.log(it.next()); // "{ value: undefined, done: true }"
console.log(it.next()); // "{ value: undefined, done: true }"
```

上面的代码定义了一个 `getIterator` 函数，这个函数可是模拟 `Iterator` 接口的实现，作用是返回一个迭代器对象

对象具有一个 `next` 方法，`next` 方法通过闭包来保存指针 `nextIndex` ,每次调用 `next` 方法 `nextIndex` 的值就会 +1，也就是指向下一个成员

然后根据 `nextIndex` 的指向返回对应的数据作为 `value` ,和 `done` 表示是否遍历完成

当 `nextIndex` 大于数组的长度时，无可用数据返回，此时 `done` 的值为 `false` 表示遍历完成

## for of 运行机制

到这里我们基本已经大概了解了 `Iterator` ,以及如何创建一个迭代器对象，但是他和 `for of` 有什么关系呢？

当 for of 执行时，循环的过程中会默认调用这个**对象上的迭代器方法** ,并依次执行迭代器对象上的 `next` 方法，`next` 返回值会赋值到 `for of` 的变量，从而输出具体的值

从上面的一句话中，我们得到一个很重要的信息，**对象上的迭代器方法**

## 默认的 Iterator 接口

ES6 规定，默认的 `Iterator` 接口部署在数据结构的 `Symbol.iterator` 属性上，`Symbol.iterator` 属性是一个迭代器方法，当这个迭代器方法执行后，就会返回一个特殊的对象 - 迭代器对象

简单来说，一个数据结构只要具有 `Symbol.iterator` 属性，就可以认为是 “可遍历的",

这就是为什么，这些对象可以被 `for of` 遍历的原因

### Symbol.iterator，它是一个表达式，返回 Symbol 对象的 iterator 属性，这是一个预定义好的、类型为 Symbol 的特殊值。

**举个栗子**

一个普通的对象是不能被 for of 遍历的

```javascript
let obj = {}

for (let k of obj){
...
}

// Error: Uncaught TypeError: obj is not iterable
那么给这个对象部署 Symbol.iterator 方法，就可以把这个对象变成一个可迭代的对象

var iterableObj = {
items: [100,200,300],
[Symbol.iterator]:function(){
let self=this;
let nextIndex = 0;
return {
next: function() {
return nextIndex < self.items.length ?
{ value: self.items[nextIndex++], done: false }
: {value: undefined, done: true}
}
};
}
}

//遍历它
for(var item of iterableObj){
console.log(item); //100,200,300
}
```

很香，上面这个对象经过部署 `Symbol.iterator` 方法后，就可以被 for of 消费了

## Iterator 原生应用场景

到了这里我们已经知道如何将一个对象改造成"可迭代对象" 了，那难道我们每次使用 for in 之前都要这样部署吗？这是似乎很麻烦

实际上 ES6 很贴心的为一些数据结构默认部署了 Iterator 接口，这些数据包括

- Array
- Map
- Set
- String
- TypeArray
- 函数的 arguments 对象
- NodeList 对象

### Array

```javascript
var arr = [100, 200, 300];

var iteratorObj = arr[Symbol.iterator](); //得到迭代器方法，返回迭代器对象

console.log(iteratorObj.next());
console.log(iteratorObj.next());
console.log(iteratorObj.next());
console.log(iteratorObj.next());
```

### 字符串

因为字符串本身是有序的，并具有类数组的特性，支持通过索引访问，所以也默认部署了 `iterator` 接口

```javascript
var str = "abc";

var strIteratorObj = str[Symbol.iterator](); //得到迭代器

console.log(strIteratorObj.next());
console.log(strIteratorObj.next());
console.log(strIteratorObj.next());
console.log(strIteratorObj.next());
```

### arguments 类数组

通过上文，我们知道对象是没有默认部署这个接口的，所以 `arguments` 这个属性没有在原型上，而是在对象自身属性上

```javascript
function test() {
  var obj = arguments[Symbol.iterator]();
  console.log(arguments.hasOwnProperty(Symbol.iterator));
  console.log(arguments);
  console.log(obj.next());
}

test(1, 2, 3);
```

我们还可以直接看原型上是否部署了这个属性即可

## 对象为什么没有默认部署

由于对象上可能有各种属性，不像数组的值是有序的，所以遍历的时候，根本没法确定遍历的先后顺序

## 不止于 for of

可以应用 iterator 接口的场景有很多，不止于 for of

- **析构赋值**

对数组和 Set 结构进行析构赋值时，会默认调用 Symbol.iterator 方法

```javascript
let set = new Set().add("a").add("b").add("c");

let [x, y] = set;
// x='a'; y='b'

let [first, ...rest] = set;
// first='a'; rest=['b','c'];
```

- **扩展运算符**

扩展运算符 (...), 也会调用默认的 Iterator 接口

```javascript
let set = new Set().add("a").add("b").add("c");

let [x, y] = set;
// x='a'; y='b'

let [first, ...rest] = set;
// first='a'; rest=['b','c'];
```

上面代码的扩展运算符内部就调用了 `Iterator` 接口

实际上，这提供了一种更简便的机制，可以将任何部署了 `Iterator` 接口的数据结构，转为数组，也就是说，只要某个数据结构部署了 Iterator 接口，就可以对它使用扩展运算符，将其转为数组

`let arr = [...iterator]`

- **yield\***

yield\* 后面跟的时一个可遍历的结构，他会调用该结构的遍历器接口

```javascript
let generator = function* () {
  yield 1;
  yield* [2, 3, 4];
  yield 5;
};

var iterator = generator();

iterator.next(); // { value: 1, done: false }
iterator.next(); // { value: 2, done: false }
iterator.next(); // { value: 3, done: false }
iterator.next(); // { value: 4, done: false }
iterator.next(); // { value: 5, done: false }
iterator.next(); // { value: undefined, done: true }
```

- **其他场合 -**
- **for ... of**
- **Array.from()**
- **Map(), Set(), WeakMap(), WeakSet()**
- **Promise.all()**
- **Promise.race()**

## 自定义迭代器 -- 还没想好怎么写

## 最后，做个总结

ES6 的出现带来了很多新的数据结构，包括 `Map` 和 `Set` ,为了统一数据获取的方法，ES6 给我们提供了 `for of` 语法糖，它是一种遍历数据的统一方式，而 `for of` 依靠 `Iterator` 实现

只有部署了 `Iterator` 接口的对象，才可以被 `for of` 遍历，这些部署了 `Iterator` 接口的对象我们称之为 - 可迭代对象

所谓的 `Iterator` 接口，实际上是可迭代对象上的一个属性 - `Symbol.iterator` 方法，执行这个方法会返回一个特殊的对象 - 迭代器对象

迭代器对象具有一个 `next` 方法，这个方法会返回一个具有 `value` 和 `done` 属性的对象，

迭代器对象内部维护一个指针，每一次调用 `next` 方法，这个指针就会指向下一个成员，从而返回下一个具有 `value` 和 `done` 属性的对象

ES6 贴心的为我们把 `Iterator` 默认部署在很多常用的数据结构中，包括 Array 、String、Map 、Set 、arguments

除了统一的数据访问方式，还可以自定义得到的数据内容，随便怎样，只要是你需要的
