---
title: "米奇记账项目实记之 SVG引入bug"
date: 2020-02-24T12:12:26+08:00
draft: false
---

### 在米奇记账项目中我使用了 svg-sprite-loader 进行SVG引入

### 现在发现了svg-sprite-loader使用中的一个bug

* #### 先说说我想达到的效果：

图中有三个icon，分别是标签、记一笔、统计，这三个icon是我从iconfont引入的SVG图标，当我点击某个icon时，被点击的icon会有高亮成红色的效果，并进入对应的导航页

* #### 目前遇到的问题是

![nav-ok-1](/images/nav-ok-1.png)

我点击了统计的icon，然后统计icon高亮红色正常，但是标签的icon我并没有设置高亮，但是却是蓝色的

当我点击标签icon时

![nav-fill](/images/nav-bug-fill.png)

标签icon依然是默认蓝色不变

* #### 解决的过程

首先我把自己对icon文件添加的SCSS都看了一遍，没看出问题

那问题估计就在SVG的源文件上了

仔细看一遍，发现SVG源码中有一个fill属性，这个属性给了icon一个默认且不可更改的颜色

![svg-fill](/images/svg-fill.png)

* #### 怎么解决

直接把fill属性手动移除，简单粗暴，但是问题来了，如果有100个SVG都带有fill属性呢，很显然这个方法太low了

那怎么解决呢？

能不能写一段代码，自动把SVG里面的fill属性移除呢？

下面是我搜了很久发现的一个答案

```javascript
.use('svgo-loader').loader('svgo-loader')
      .tap(options => ({...options, plugins: [{removeAttrs: {attrs: 'fill'}}]})).end()
```

