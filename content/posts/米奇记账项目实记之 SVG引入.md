---
title: "米奇记账项目实记之 SVG引入"
date: 2020-02-21T12:12:26+08:00
draft: false
---

## 先简单介绍一下SVG

### 什么是SVG

SVG全称是Scalable Vector Graphics，是指伸缩矢量图片。我们常用的png，jpg图片属于位图，位图是由一个个像素组成的，放大后会变模糊。而SVG是通过软件生成的矢量图片，其元素对象可编辑，图像放大缩小都不影响图像的分辨率。

### SVG的优点

* SVG 是一种基于 XML 语法的图像格式，所以在前端项目中我们可以进行二次编辑，可以修改颜色，大小....
* SVG 于JPEG 和 GIF 图像比起来，尺寸更小
* SVG 图像的文本是可选的，同时也是可搜索的

所以我认为SVG很适合在前端项目中应用

## 怎么引入SVG

最近我在开发一个个人理财项目，底部导航栏需要用到SVG图标，设计稿是这样的：

![nav-1](/images/nav-design.png)

* #### 第一步通过iconfont搜索相应的图标

[iconfont](https://www.iconfont.cn/home/index)是一个阿里巴巴提供的矢量图库，可以免费下载SVG图标，下载后我把SVG图标存在项目的assets/icons文件夹中

* #### 第二步参考iconfont提供的引入方法

[iconfont的帮助文档](https://www.iconfont.cn/help/detail?spm=a313x.7781069.1998910419.16&helptype=code)，我是用的是symbol引入，这是官方文档提供的方法

![iconfont](/images/iconfont.png)

由于我引入的下载后的SVG文件，js通过import引入

```
import '@/assets/icons/label.svg'
import '@/assets/icons/money.svg'
import '@/assets/icons/statistics.svg'
```

HTML 和 CSS 照文档敲，要注意的是 xlink:href="#icon-xxx" ,icon-xxx是SVG文件名，敲错了就无法显示了

按照官方文档做完了，按理来说SVG就应该显示了，然而导航栏还是一片空白

![nav-none](/images/nav-none.png)

* #### 第三步安装svg-sprite-load

于是开始上网查，发现是缺少了 svg-sprite-load

安装命令:

```
npm i svg-sprite-loader --save
或者
yarn add svg-sprite-loader --dev
```

* #### 第四步配置vue.config.js

在vue.config.js 中添加

```
config.module
      .rule('svg-sprite')  // 找到svg-loader
      .test(/\.svg$/) 
      .include.add(dir).end() 
      .use('svg-sprite-loader').loader('svg-sprite-loader').options({extract:false}).end()
    config.plugin('svg-sprite').use(require('svg-sprite-loader/plugin'), [{plainSprite: true}])
    config.module.rule('svg').exclude.add(dir) 
```

安照上面的代码在webStorm中运行，有的小伙伴会遇到这个报错 （在require语句的地方）

```
ESLint: Require statement not part of import statement.(@typescript-eslint/no-var-requires)
```

#### 有两种解决方式

1. #### 按照误提示改为 import 引入

```
import svgLoader from 'svg-sprite-loader/plugin';
// 然后在使用require的地方替换为 svgLoader
config.plugin('svg-sprite').use(svgLoader, [{plainSprite: true}])
```

2. #### 告诉eslint 不检查这行代码（在报错的代码行上一行插入）

```
// eslint-disable-next-line @typescript-eslint/no-var-requires
```

现在再次运行代码，SVG图片可以显示了

![nav-ok](/images/nav-ok.png)

* #### 第五步优化代码之引入文件

上文中js引入SVG文件是通过import，一个一个文件引入的，以后如果svg越来越多就要多次引入。

于是我在网上找了一个方法可以直接引入icons文件，这样就不用每个icon多次引入了，代码如下

```
const importAll = (requireContext: __WebpackModuleApi.RequireContext) => requireContext.keys().forEach(requireContext);
  try {
    importAll(require.context('../assets/icons', true, /\.svg$/));
  } catch (e) {
    console.log(e);
  }
```

* #### 第六步优化代码之封装成Icon组件

按照文档所说，HTML使用的标签是

```
<svg class="icon" aria-hidden="true">
    <use :xlink:href= "'#' + name" />
</svg>
```

那我用到了三个SVG图标，我就要重复三次，代码就太丑了，我们可以封装成Icon组件

```
<template>
  <svg class="icon" aria-hidden="true">
    <use :xlink:href= "'#' + name" />
  </svg>
</template>

<script lang='ts'>
  export default {
    props: ['name']
  };
</script>

<style lang='scss' scoped>
  .icon {
    width: 1em; height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
  }
</style>
```

并在main.ts 中全局引入

```
import Vue from 'vue';
import Icon from '@/components/Icon.vue';

Vue.component('Icon', Icon);
```

这样每个组件使用SVG图标时，只需要即可

```
<Icon name="SVG文件名"/>
```



感谢看官们看到最后，目前使用svg-sprite-loader 发现有些bug，会在后续文章中更新解决方法