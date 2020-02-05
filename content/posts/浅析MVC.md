---
title: "浅析 MVC"
date: 2020-01-31T12:12:26+08:00
draft: false
---


![img1](/images/MVC.jpg)

### MVC是三个字母的缩写，分别是Model（模型）、View（视图）和Controller（控制）

这个模式认为，程序不论简单或复杂，从结构上看，都可以分成三类对象。

1.模型model用于封装与应用程序的业务逻辑相关的数据以及对数据的处理方法，会有一个或多个视图监听此模型。一旦模型的数据发生变化，模型将通知有关的视图。

```
// 伪代码示例
Model = {
    data: { 程序需要操作的数据或信息 }，
    create: { 增数据 },
    delete: { 删数据 },
    update(data) { 
       Object.assign(m.data, data) //使用新数据替换旧数据
       eventBus.trigger('m:upate') // eventBus触发'm:update'信息, 通知View刷新 
    },
    get:{ 获取数据 } 
}
```

2.视图view是屏幕上的表示，描绘的是model的当前状态。当模型的数据发生变化，视图相应地得到刷新自己的机会

```
// 伪代码示例
View = {
    el: 需要刷新的元素,
    html: `<h1>M V C</h1>....显示在页面上的内容`
    init(){
        v.el: 需要刷新的元素
    },
    render(){ 刷新页面 }
}
```

3.控制器controller定义用户界面对用户输入的响应方式，起到不同层面间的组织作用，用于控制应用程序的流程，它处理用户的行为和数据model上的改变。
```
// 伪代码示例
Controller = {
   init(){
      v.init() // View初始化
      v.render() // 第一次渲染
      c.autoBindEvents() // 自动的事件绑定
      eventBus.on('m:update', () => { v.render() }) // 当eventBus触发'm:update'时View刷新
   },
   events:{ 事件以哈希表方式记录 }，
   method() {
      data = 改变后的新数据
      m.update(data)
   }，
   autoBindEvents() { 自动绑定事件 }
}EventBus
```

### EventBus的作用

* 模块通信

`解决模块之间通信的问题，view组件层面，父子组件、兄弟组件通信都可以使eventbus处理`

* 模块解耦
  
`storage change事件，cookie change事件，view组件的事件等，全部转换使用Event Bus来订阅和发布，这样就统一了整个应用不同模块之间的通信接口问题。`

* 父子页面通信

`window.postMessage + Event Bus`

* 多页面通信

`storage change + Event Bus`

#### EventBus的一些常用api

* on（监听事件）
* trigger（触发事件）
* off（取消监听）

```
eventBus.trigger('m:updated') // 触发事件,大叫'm 已经更新了'
eventBus.on('m:updated', () => { console.log('here') }) '监听事件，听
到后执行函数'
```

### 表驱动编程

表驱动法是一种编程模式，从表(哈希表)里面查找信息而不是使用逻辑语句（if…else…switch），当是很简单的情况时，用逻辑语句很简单，但如果逻辑很复杂，再使用逻辑语句就很麻烦了。

```
// 举个栗子
events:{  // 事件集合的哈希表
   'click #app1': 'a操作'，
   'click #app2': 'b操作'，
   'click #app3': 'c操作'，
   'click #app4': 'd操作'，
} 
autoBindEvents() { // 通过哈希变自动绑定事件
    for ( let key in c.events) {
        if ( c.events.hasOwnProperty(key) ) {
            const spaceIndex = key.indexOf(' ')   // 找到'click #app1'空格的数组下标
            const part1 = key.slice(0, spaceIndex) // 通过spaceIndex 分割 'click' 和 '#app1'
            const part2 = key.slice(spaceIndex + 1)
            const value = c[c.events[key]]
            v.el.on(part1, part2, value)  // 自动把多个事件绑定在一个元素上
        }
    }
}
// 如何一来在稳定的复杂度里，可以绑定多个事件
```

### 对模块化的理解
在一个完整的web页面中，通过不同节点的功能，结构的不同，可以分出多个模块，而每个模块的实现方式和使用的技术等等都不相同，引入模块化，可以切断每个模块的相互影响，使得单个模块中可以更好的优化代码。

模块化可以降低代码耦合度，减少重复代码，提高代码重用性，并且在项目结构上更加清晰，便于维护