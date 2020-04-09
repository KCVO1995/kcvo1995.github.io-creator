---
title: "米奇记账项目总结"
date: 2020-03-01T12:12:26+08:00
draft: false
---

初学 Vue 和 TypeScript，想着准备一个小项目为自己找工作打打气，自己之前一直嚷嚷着要好好记账，于是从自身的需求出发，就有了米奇记账。

### 已部署的版本

点这里，点这里：[米奇记账](https://kcvo.top/morney-website/#/home)

### 源代码

[Github](https://github.com/KCVO1995/money)

# 项目介绍

米奇记账是一个基于 Vue 的单页面应用，简约的 UI 设计，支持账目的增删改查，标签管理及新增，账目数据直观展示，支持按月、按周、按天展示。

米奇记账一共有这样几个页面：

- 首页：显示当月收入和支出，显示当日收入和支出，可跳转到所有其他页面
- 记账页面：支持单笔记账的标签选择，备忘记录，金额记录
- 标签管理页面：展示已有标签，可选择删除，可选新增标签
- 新增标签页面：提供 20 个带精美图标的常用标签，还可以新建自定义标签
- 账目明细页面：提供月份选择器，周期选择器，日期选择器，按需暂时账目，另外可以删除或修改已有账目

# 技术栈

- #### Vue
- #### Vue Cli
- #### Vue Router
- #### Vuex
- #### TypeScript
- #### Sass
- #### dayjs
- #### UI

# 思路与经验

## 如何按照不同的时间单位显示金额

对于记账项目，记账金额需要按照不同的时间单位显示，例如按月，按周，按日，如果每次都要写一个函数进行显示，显然是繁琐的，于是我想出了一个可以复用的显示金额函数

```javascript
const showAmount = (type: string, unit: Unit, according: any, record: boolean) => {
  const recordList = clone(store.state.recordList).filter(record => record.type === type);
  if (recordList.length === 0) {return 0;}
  const recordGroup = [];
  let total = 0;
  for (let i = 0; i < recordList.length; i++) {
    const recordCreateAt = dayjs(recordList[i].createAt);
    if (dayjs(recordCreateAt).isSame(according, unit)) {
      recordGroup.push(recordList[i]);
    }
  }
  if (record) {return recordGroup;}
  for (let i = 0; i < recordGroup.length; i++) {total += recordGroup[i].amount;}
  return total;
};
```

showAmount 函数接受多个参数，

- type 对应的是 "收入" 和 "支出" 两种类型
- unit 对应的是要对比的时间单位
- according 是用于对比的日期
- record 接受布尔值，true 代表返回相应的记录,false 代表返回统计的金额

首先从 Vuex 的 state 中获取到`recordList`是账目数组，并通过 "收入" 和 "支出" 类型进行分类，如果得到的 `recordList.length`为零，直接返回 0

然后通过 for 循环遍历`recordList`,将`record.createAt`创建时间改为 Day.js 格式，使用 Day.js 的`isSame`api 按照不同时间单位`unit`和`according`进行对比，将配对成功的`record`通过数组的 push 到`recordGroup`中

通过`record`参数判断，返回的是`record`对象，还是统计金额

如果是统计金额，通过 for 循环遍历`recordGroup`,将每个`record`的金额进行相加,然后返回总额

# Home 页面

## 首页和记账页面的通信

主页中有 "+" 和 "-" 两按钮，分别对应的是 "收入" 和 "支出"，点击这两个按钮就会跳转到记账页面，因为记账记录是在记账页面中进行提交的，所以我要做的是把选择的类型告诉记账页面组件

首页对应的是 Home 组件，记账页面对应的是 Money 组件

两个组件时兄弟组件，所以我选择的通信方式是使用`eventBus`

首先我在 App.vue 中提供一个`eventBus`

```javascript
@Provide('eventBus') eventBus = new Vue();
```

然后在 Home.vue 中注入`eventBus`,并在跳转后通过`eventBus`发布 "update:type"

在`this.$nextTick()`中发布的原因是，`eventBus`监听时间是 Money 组件创建后，所以要推迟发布的时间

```javascript
@Inject() eventBus: any;
@Prop(Number) selectedMonth: number | undefined;

toMoney(type: string) {
  this.$router.push('/money/0');
  this.$nextTick(() => {
  this.eventBus.$emit('update:type', type);
  });
}
```

最后在 Money 组件中进行订阅 "update:type",并在组件创建完成后，赋值到 Money 组件的`type`数据中

```javascript
created() {
  this.eventBus.$on('update:type', (type: string) => {
  this.reset.type = type;
  });
}
```

上述代码使用的都是 TS 装饰器的写法

# 记账页面

## 如何将记账记录存入 localStorage

记账页面对应的是 Money 组件

Money 组件中，我先对`record`进行初始化

```javascript
record = {id: 0, selectedTags: [], notes: '', type: '-', amount: 0}
```

然后再用户点击页面的提交按钮时，记录要 push 进`recordList` 数组然后存入`localStorage`

但是如果直接将`record`,push 到`recordList`是不可以的，因为这样存的只是对象的地址，这样就会存在一个问题，点击提交后，页面的数据是要清零的，方便用户再次记账，这样就会影响到上次的`record`

所以我们需要先对`record`进行深拷贝，再 push 到`recordList`

于是我写了一个用于深拷贝的`clone`函数

```javascript
function clone(data) {
  return JSON.parse(JSON.stringify(data));
}
```

在存记账记录时，我们可以直接调用`clone`函数

```javascript
createRecord(state, record) {
  record.createAt = new Date().toISOString();
  const newRecord = clone(record);
  state.recordList.push(newRecord);
  store.commit('saveRecords');
  },
  saveRecords(state) {
    localStorage.setItem('recordList', JSON.stringify(state.recordList));
  }
```

# 标签管理页面

## 如何实现 ID 生成器

要实现标签的增删改查，我们需要个每个标签一个独一无二的 ID，于是我写了这样一个 ID 生成器

```javascript
let id = parseInt(localStorage.getItem('_idMax') || '0') || 0;
const createId = () => {
  id++;
  localStorage.setItem('_idMax', JSON.stringify(id));
  return id;
};
```

首先从`localStorage` 中提取 id 最大值`_idMax`，如果没有则使用 0

运行`createId`，id 加 1，并把目前 id 存为 id 最大值，返回 id

下一次运行时，得到上一次的`_idMax`，加 1，再存为 id 最大值，返回新 id

# 统计页面

## 如何实现日期选择器

我认为实现日期选择器要分为两部分，第一要按周期规律显示，第二实现可选择

### 按周期规律显示

在米奇记账中，日期的显示和日历类似，可以让人方便选择，预览如下

![日期选择器](https://pic3.zhimg.com/80/v2-df2c5369cdc80e5326eefb2721f14cba_720w.jpg)

所以我们要先对日期进行处理，才能展示在页面上

我的想法时，周一到周日为一周，并将同月的每一周的日期组成一个数组

由于每月的第一天和最后一天，并非周一和周日，这样第一周的数组和最后一周的数组可能是不完整的，所以需要再加工，对于第一周将上一月的数据进行补全，对于最后一周用下个月的数据进行补全

这是实现的代码：

```javascript
createDay(month: number) {
  const daysInMonth = this.getDaysInMonth(month);
  const hashTable = {};
  let weeks = 1;
  for (let i = 1; i <= daysInMonth; i++) {
    const date = dayjs().set('month', month).set('date', i);
    hashTable[weeks] = hashTable[weeks] || [];
    hashTable[weeks].push(date);
    if (date['$W'] === 0) {weeks++;}
  }
  this.complement(hashTable, month);
  return hashTable;
}
```

首先拿到当前月的天数

声明一个 hashTable 用于储存数据

声明 weeks 赋值为 1，作为日期分组的 key

通过 for 循环，遍历次数是月的天数，将获得的第一天，加入 key 为第一周的数组中

当日期为周天的时候，weeks 加一，也就是增加一周

```javascript
complement(hashTable: dateGroup, month: number) {
  const daysInLastMonth = this.getDaysInMonth(month - 1);
  const last = hashTable[Object.keys(hashTable).length];
  const first = hashTable[1];
  if (first.length < 7) {
    const delta = 7 - first.length;
    for (let i = 0; i < delta; i++) {
      const date = dayjs().set('month', month - 1).set('date', daysInLastMonth - i) as never;
      first.unshift(date);
    }
  }
  if (last.length < 7) {
    const delta = 7 - last.length;
    for (let i = 0; i < delta; i++) {
      const date = dayjs().set('month', month + 1).set('date', i + 1) as never;
      last.push(date);
    }
  }
}
```

这个函数是对于第一周和最后一周，不完整的数组进行补全

首先获取上一月的天数

获取第一周，判断长度，如果小于 7 就是没有完整周的日期，需要补全，通过上月的最后几天进行补全

获取最后一周，判断长度，如果小于 7 就是没有完整周的日期，需要补全，通过下月的前几天进行补全

# 其他问题

## 怎么引入 SVG

在这个项目中多次使用的了 SVG 图片，SVG 全称是 Scalable Vector Graphics，是指伸缩矢量图片，比起 png，jpg 图片，它有很多优点，它是一种基于 XML 语法的图像格式，在项目中我们可以进行二次修改，可以修改颜色，大小，而且它的体积更小。

项目中使用的 SVG 图标，我都是从 iconfont 引入的

按照官方文档填入

HTML

```html
<svg class="icon" aria-hidden="true">
    <use xlink:href="#icon-label"></use>
</svg>
```

CSS

```css
<style type="text/css">
    .icon {
       width: 1em; height: 1em;
       vertical-align: -0.15em;
       fill: currentColor;
       overflow: hidden;
    }
</style>
```

JS

```javascript
import '@/assets/icons/label.svg'
```

发现 SVG，还是没有显示，于是我发现是缺了 svg-sprite-load

解决方法是：

首先安装

```bash
npm i svg-sprite-loader --save
或者
yarn add svg-sprite-loader --dev
```

然后配置 vue.config.js

```javascript
config.module
      .rule('svg-sprite')  // 找到svg-loader
      .test(/\.svg$/)
      .include.add(dir).end()
      .use('svg-sprite-loader').loader('svg-sprite-loader').options({extract:false}).end()
    config.plugin('svg-sprite').use(require('svg-sprite-loader/plugin'), [{plainSprite: true}])
    config.module.rule('svg').exclude.add(dir)
```

安照上面的代码在 webStorm 中运行，发现这个报错 （在 require 语句的地方）

```
ESLint: Require statement not part of import statement.(@typescript-eslint/no-var-requires)
```

我发现有两种解决方式

1. 安装错误提示改为 import 引入

```javascript
import svgLoader from 'svg-sprite-loader/plugin';
// 然后在使用require的地方替换为 svgLoader
config.plugin('svg-sprite').use(svgLoader, [{plainSprite: true}])
```

2. 告诉 eslint 不检查这行代码（在报错的代码行上一行插入）

```javascript
// eslint-disable-next-line @typescript-eslint/no-var-requires
```

详细的博客记录，请移步：[米奇记账项目实记之 SVG 引入 bug](https://kcvo.top/2020/%E7%B1%B3%E5%A5%87%E8%AE%B0%E8%B4%A6%E9%A1%B9%E7%9B%AE%E5%AE%9E%E8%AE%B0%E4%B9%8B-svg%E5%BC%95%E5%85%A5/)

## SVG 引入遇到的 bug

在使用 SVG 图标的过程中，发现有些 SVG 图标，在 SCSS 设置`fill`颜色后，颜色依然不变。

于是我首先检查了 SCSS 代码后，发现没问题

那问题可能就在 `SVG` 的源文件上了

仔细看了一篇发现 SVG 源码中有一个 fill 属性，这个属性给了 icon 一个默认且不可更改的颜色

解决的方法：

1. 手动把 `fill` 属性移除，简单粗暴，但是问题来了，如果有 100 个 SVG 都带有 fill 属性呢，很显然这个方法太 low 了

2. 是一个我搜了很久的答案

```javascript
.use('svgo-loader').loader('svgo-loader')
      .tap(options => ({...options, plugins: [{removeAttrs: {attrs: 'fill'}}]})).end()
```

详细的博客记录，请移步：[米奇记账项目实记之 SVG 引入 bug](https://kcvo.top/2020/%E7%B1%B3%E5%A5%87%E8%AE%B0%E8%B4%A6%E9%A1%B9%E7%9B%AE%E5%AE%9E%E8%AE%B0%E4%B9%8B-svg%E5%BC%95%E5%85%A5bug-copy/)

