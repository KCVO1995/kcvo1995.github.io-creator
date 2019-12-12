---
title: "HTML常用标签"
date: 2019-12-12T16:23:26+08:00
draft: false
---

## a 标签的用法

* ### a 的href取值
1. #### 网址
*  https  `<a href="https//google.com">谷歌</a>`
* http  `<a href="http//google.com">谷歌</a>`

* //  `<a href="//google.com">谷歌</a>`

    以上三种形式均可

1. #### 路径
   
    相对路径和绝对路径都可以.

2. #### 伪协议
   
* javascript,可以引入JS代码

    `<a href="javascript:alert(1);">javascript</a>`

* mailto,点击后跳转到发邮件页面

    `<a href="mailto:943514589@qq.com">send mail to</a>`

    以下是mailto的详细用法

    ![mailto](/images/mailto.png)

* tel,手机点击后跳转到呼叫页面

    `<a href="tel:13311113322">phone call</a>`

4. #### id

    id,可以跳转到页面内id所标记的元素位置

    `<a href="#xxx">查看ID为xxx的项目</a>`

<hr/>

* ### target的取值

1. #### 内置命名

* 取值为_blank,a标签的链接在新窗口打开

* 取值为_top,a标签的链接在最顶层的窗口打开

* 取值为_parent,a标签的链接在其上一层的窗口打开

* 取值为_self,a标签的链接在当前窗口打开


2. #### 程序员命名

* 取值为window的name,a标签的链接会在对应名称的窗口打开

* 取值为iframe的name,a标签的链接会在对应名称的内嵌窗口打开


3. #### download

* 下载a标签的目标链接

## img 标签的用法

* ### 作用

    img的作用是发出get请求,展示一张照片

* ### 属性

* alt 当图片请求失败是,显示alt内容
* height 设定图片高度,单位默认px
* width 设定图片宽度,单位默认px
* src 填写资源地址

* ### 事件

* onload,图片加载成功时触发事件

* onerror,图片加载失败时触发事件

* ### 响应式

    响应式页面,可以适合不同屏幕及手机端查看,需要加上max-width: 100%

* ### 可替换元素

    可替换元素（replaced element）的展现效果不是由 CSS 来控制的。这些元素是一种外部对象，它们外观的渲染，是独立于 CSS 的。

## table 标签的用法

* ### table的基本结构
```  
<table>
    <thead>
        <tr>
            <th>英语</th>
            <th>翻译</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>hyper</td>
            <td>超级</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td>nothing</td>
            <td>nothing</td>
        </tr>
    </tfoot>
</table>
```
thead 表头 ；tbody 表身 ；tfoot 表尾；
包含在以上三个属性中的有tr 一行、th会加粗、td不会加粗。

* ### table的相关样式

1. #### table-layout

   *  table-layout取值为fixed时,每列宽度等宽
  
   *  table-layout取值为auto时,每列宽度根据其内容宽度设定

2. #### border-collapse,此属性可以消除表格边界之间的距离

3. #### border-spacing,此属性可以修改表格边界之间的距离

## 最后写一点想法

* ### html标签十分之多,但并不是所用标签都能经常用到,所以不需要每个标签逐个学习,只需要先把常用的学习了,剩下的在工作中遇到再学习

* ### html标签的默认样式,大部分已经过时(不符合现在的审美),建议在编写网站时先把html标签的默认样式取消掉




