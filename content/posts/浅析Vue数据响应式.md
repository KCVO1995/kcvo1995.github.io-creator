---
title: "浅析Vue数据响应式"
date: 2020-02-05T12:12:26+08:00
draft: false
---

### Vue的数据存储在data里，当数据更新时，会触发视图的渲染。

首先先来看一下Vue到底对data做了什么。

```
//引用完整版 Vue
import Vue from "vue/dist/vue.js"; 

Vue.config.productionTip = false;  //禁用警告

const myData = {
  n: 0
}
console.log(myData)  // 精髓

let vm = new Vue({
  data: myData,
  template: `
    <div>{{n}}</div>
  `
}).$mount("#app");

setTimeout(()=>{
  myData.n += 10
console.log(myData)  // 精髓
},3000)
```
![](/images/vuelog.png)

#### 两次打印出的结果不同，说明vue对data里的数据进行了一些改造

## ES6 的 getter 和 setter

#### getter 和 setter 可以对虚拟属性进行性改写

请看下面的例子

### 需求一、得到姓名

```
let obj0 = {
  姓: "迪",
  名: "丽热巴",
  age: 18
}

// 需求一，得到姓名

let obj1 = {
  姓: "迪",
  名: "丽热巴",
  姓名() {
    return this.姓 + this.名;
  },
  age: 18
};

console.log("需求一：" + obj1.姓名()) // 需求一：迪丽热巴

// 抬杠：obj1.姓名() 可以去到括号吗？ 
// 不可以，因为姓名时函数
```

### 需求二、姓名不要括号也可运行

```
let obj2 = {
  姓: "迪",
  名: "丽热巴",
  get 姓名() {
    return this.姓 + this.名
  },
  age: 18
};
console.log("需求二：" + obj2.姓名) // 需求一： 迪丽热巴

// 姓名()是虚拟属性
```

#### 通过get ，给属性提供一个getter方法，当该属性被访问时，该方法会被执行

### 需求三、姓名要可以改写

```
let obj3 = {
  姓: "迪",
  名: "丽热巴",
  get 姓名() {
    return this.姓 + this.名;
  },
  set 姓名(xxx){
    this.姓 = xxx[0]
    this.名 = xxx.slice(1)//或者this.名 = xxx.substring(1)
  },
  age: 18
};
obj3.姓名 = '古力娜扎'

console.log(`需求三：姓 ${obj3.姓}，名 ${obj3.名}`) // 需求三： 古力娜扎

// 姓名()是虚拟属性
```

#### 通过set，给属性提供一个setter方法,当属性值修改时，触发执行该方法

#### 此时我们看看obj3的结构

![Vue](/images/vuelog2.png)


#### 是不是和上文的vue处理后myData结构很类似，说明vue把myData的'n'改为虚拟属性，通过setter 和 getter 进行改写。


#### getter 和 setter 我知道了，可是这样改有什么用呢？我们需要先学习一下

## Object.defineProperty

请看下面的例子

```
let data0 = {
  n: 0
}
```

### 需求一：用Object.defineProperty

```
let data1 = {}

Object.defineProperty(data1, 'n', {
  value: 0
})

console.log(`需求一：${data1.n}`)  // 需求一：0

// 总结： 这煞笔语法很好的化简为繁了，其实不是，请继续看
```

### 需求二：n 不能小于 0

#### 即 data2.n = -1 应该无效，但data2.n = 1 有效

```
let data2 = {}

data2._n = 0 // _n 用来偷偷存储 n 的值

Object.defineProperty(data2, 'n', {
  get(){
    return this._n
  },
  set(value){
    if(value < 0) return
    this._n = value
  }
})

console.log(`需求二：${data2.n}`) // 需求二： 0
data2.n = -1
console.log(`需求二：${data2.n} 设置为 -1 失败`)  // 需求二： 0 设置为 -1 失败
data2.n = 1
console.log(`需求二：${data2.n} 设置为 1 成功`)  // 需求二： 0 设置为 1 成功

// 抬杠：那如果对方直接使用 data2._n 呢？
// 算你狠
```

### 需求三：使用代理

```
let data3 = proxy({ data:{n:0} }) // 括号里是匿名对象，无法访问

function proxy({data}/* 解构赋值 */){
  const obj = {}
  Object.defineProperty(obj, 'n', { 
    get(){
      return data.n
    },
    set(value){
      if(value<0)return
      data.n = value
    }
  })
  return obj // obj 就是代理
}

// data3 就是 obj
console.log(`需求三：${data3.n}`) // 需求三： 0
data3.n = -1
console.log(`需求三：${data3.n}，设置为 -1 失败`) // 需求三： 0 设置为 -1 失败
data3.n = 1
console.log(`需求三：${data3.n}，设置为 1 成功`) // 需求三： 1 设置为 1 成功

// 杠精你还有话说吗？
// 杠精说有！你看下面代码
// 需求四

let myData = {n:0}
let data4 = proxy({ data:myData }) // 括号里是匿名对象，无法访问

// data3 就是 obj
console.log(`杠精：${data4.n}`) // 杠精： 0
myData.n = -1
console.log(`杠精：${data4.n}，设置为 -1 失败了吗！？`) // 杠精：设置为 -1 失败了吗！？  

// 我现在改 myData，是不是还能改？！你奈我何
// 算你狠
```

### 需求五：就算用户擅自修改 myData，也要拦截他

```
let myData5 = {n:0}
let data5 = proxy2({ data:myData5 }) // 括号里是匿名对象，无法访问

function proxy2({data}/* 解构赋值 */){
  let value = data.n
  Object.defineProperty(data, 'n', {
    get(){
      return value
    },
    set(newValue){
      if(newValue<0)return
      value = newValue
    }
  })
  // 就加了上面几句，这几句话会监听 data

  const obj = {}
  Object.defineProperty(obj, 'n', {
    get(){
      return data.n
    },
    set(value){
      if(value<0)return //这句话多余了
      data.n = value
    }
  })
  
  return obj // obj 就是代理
}

// data3 就是 obj
console.log(`需求五：${data5.n}`)
myData5.n = -1
console.log(`需求五：${data5.n}，设置为 -1 失败了`)
myData5.n = 1
console.log(`需求五：${data5.n}，设置为 1 成功了`)
```

#### 看看这代码眼熟吗？

```
let data5 =proxy2({ data:myData5 })

let vm = new Vue({data: myData})
```

## 现在我们可是说Vue对myData做了什么了

* #### 给对象添加value属性，添加getter 和 setter 对属性进行监控
* #### 使用vm作为myData的代理
* #### 会对myData的所有属性进行监控，当vm知道myData属性变了，就可以调用render(data)进行渲染了。
  
## 数据响应式

`const vm = new Vue({ data: { n: 0 } })`

如果我修改vm.n，那么UI中的n就会响应我

Vue 2 通过Object.defineProperty来实现数据响应式

## Vue 有 bug

Object.defineProperty的问题

Object.defineProperty(obj, 'n' ,{..})必须要有一个'n',才能监听&代理obj.n

那如果没有给出n呢？

请看下面的示例：


示例一：

```
import Vue from "vue/dist/vue.js";

Vue.config.productionTip = false;

new Vue({
  data: {},
  template: `
    <div>{{n}}</div>
  `
}).$mount("#app");
Vue会给出一个警告
```


示例二：

```
import Vue from "vue/dist/vue.js";

Vue.config.productionTip = false;

new Vue({
  data: {
    obj: {
      a: 0 // obj.a 会被 Vue 监听 & 代理
    }
  },
  template: `
    <div>
      {{obj.b}}
      <button @click="setB">set b</button>
    </div>
  `,
  methods: {
    setB() {
      this.obj.b = 1; 
    }
  }
}).$mount("#app");
```

#### 此时点击setB后，页面会显示1吗？


答案是：不会，因为Vue无法监听一开始就不存在的obj.b

### 解决方法：

```
// 一、一开始就声明好所有的key
new Vue({
  data: {
    obj: {
      a: 0 ；
      b:''
 }
 },
// 二、使用Vue.set 和 this.$set
methods: {
    setB() {
        Vue.set( this.obj, 'b', 1) // 或者 this.$set(this.obj, 'b', 1)
    }
}
```

### 总结：

#### 由于Object.definePropery 的限制，Vue无法检测到对象属性的添加或删除。所以Vue不允许动态添加根级响应式属性，所以你必须在初始化实例前声明所有根级响应式属性，哪怕只是一个空值，或者使用Vue.set 或 vm.$set 进行设置。

## 当data是数组时：

请看示例

```
import Vue from "vue/dist/vue.js";

Vue.config.productionTip = false;

new Vue({
  data: {
    array: ["a", "b", "c"]
  },
  template: `
    <div>
      {{array}}
      <button @click="setD">set d</button>
    </div>
  `,
  methods: {
    setD() {
      this.array[3] = "d"; //请问，页面中会显示 'd' 吗？
      // 等下，你为什么不用 this.array.push('d')
    }
  }
}).$mount("#app");
```

#### 如果data时数组，意味着你无法提前声明所有key，也不可以一直使用Vue.set 或 vm.$set 进行设置。

#### 尤雨溪的做法是，篡改数组的API，我们可以使用Vue提供的变异方法操作，如下:

this.array.push('value')
这里使用的push并不是Array.push，而是经过vue篡改后的api，vue的data中的数组会增加一层原型，实现了对数组数据的监听和代理，从而触发视图的更新。在Vue中这种api称为变异方法。这些变异方法包括：

* push()
* pop()
* shift()
* unshift()
* splice()
* sort()
* reverse()