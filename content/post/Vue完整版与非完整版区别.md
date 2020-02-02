---
title: "Vue完整版与Vue非完整版的区别"
date: 2020-02-02T12:12:26+08:00
draft: false
---
* #### Vue完整版

Vue完整版文件名为 vue.js 和 vue.min.js , 后者是压缩版，取消了注释和警告，适合在生产环境下使用。

* #### Vue非完整版

Vue非完整版文件名为 vue.runtime.js 和 vue.runtime.min.js， 后者是压缩版，取消了注释和警告，适合在生成环境下使用。

## Vue完整版与Vue非完整版的区别

![版本区别](/images/different.png)

## template 和 render 怎么用

* #### template (完整版使用)

```
// 需要编译器
new Vue({
  template: '<div>{{ hi }}</div>'
})
```

* #### render (完整版与非完整使用)

```
// 不需要编译器
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})
```

## 完整代码和非完整版如何选择

个人建议：选择非完整版，然后配合vue-loader和vue文件。

#### why：

1. 保证用户体验，用户下载的JS文件体积更小(小30%)，但只支持h函数。
2. 保证开发体验，开发者可以直接在vue文件里写HTML标签，而不用写h函数。
3. 累活让vue-loader来做，vue-loader把vue文件里的HTML转为h函数。


## 如何用 codesandbox.io 写 Vue 代码

#### [codesandbox.io](https://codesandbox.io/)​


* #### 第一步，create a Sandbox

![first](/images/first.png)

* #### 第二步，选择Vue

![second](/images/second.png)

* #### 第三步，开始撸代码吧 （Sandbox 使用的是非完整版）

![third](/images/third.png)
