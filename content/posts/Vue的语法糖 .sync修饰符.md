---
title: "Vue的语法糖 .sync修饰符"
date: 2020-02-06T15:12:26+08:00
draft: false
---

### 看完这个栗子就明白了：

```
<template>
  <div style='border: 3px solid red;padding: 10px;'>
    App.vue 我现在有 {{total}}
    <hr>
    <Child :money="total" v-on:update:money = "total = $event"  />
    <!-- $event 保存了$emit 传入的参数money-100 -->
  </div>
</template>


<script>
import Vue from "vue"

Vue.component('Child', {
  template: `<div style='border: 3px solid green'>
                {{money}}
            <button @click="$emit('update:money', money-100)" >
            <span>花钱</span>
            </button>
            </div>`,
  props: ["money"]
})

export default {
  data() {
    return { total: 10000 };
  },
}
</script>
```

这个例子中，点击‘花钱’按钮时，子组件会通知父组件，‘我要花钱 money - 100’，父组件知道后修改total。完成花钱动作。这个一个完整的发布订阅模式。

### 使用.sync 语法糖

`<Child :money.sync="total" />`

#### 等同于

`<Child :money="total" v-on:update:money = "total = $event"  />`

### vue 修饰符sync的功能是：当一个子组件需要改变了一个 prop 的值时，会通知其父组件进行同步的修改。