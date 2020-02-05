---
title: "CSS动画优化"
date: 2019-12-19T15:23:26+08:00
draft: false
---
# 浏览器渲染原理
![渲染树](/images/randertree.png)
浏览器渲染经过以下步骤:
1. 根据HTML构建HTML树。(DOM)
2. 根据CSS构建CSS树。(CSSOM)
3. 将两棵树合并成一颗渲染树。(rander tree)
4. Layout布局(文档流、盒模型、计算大小和位置)。
5. Paint绘制(把边框颜色、文字颜色、阴影等画出来)。
6. Compose合成(根据层叠关系展示画面).

# 如何优化CSS动画
### 答案是:减少浏览器的渲染次数

三种的更新方式:
![三种更新方式](/images/3ways.png)

可见三种更新方式中,第一种更新方法需要重新渲染3次(包括layout,paint,compose),第二种需重新渲染2次,而第三种只需要重新渲染1次.

不同的CSS属性有不同的更新方式,大家可以参考csstriggers.com(不需硬记),这个网站已经把所有属性都试过了.

Tips: 参考网站中的Blink为谷歌浏览器内核,Gecko为火狐浏览器内核,WebKit为Safari浏览器内核,EdgeHTML为IE浏览器内核

所以优化CSS动画性能的方法是尽量使用渲染次数少的CSS属性

* CSS优化:使用will-change或translate
* JS优化:使用requestAnimaitonFrame代替setTimeout或setlnterval
