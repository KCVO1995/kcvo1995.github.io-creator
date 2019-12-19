---
title: "负margin的理解"
date: 2019-12-15T15:23:26+08:00
draft: false
---

## margin的概述

margin属性为给定元素设置所有四个（上下左右）方向的外边距属性。这是四个外边距属性设置的简写。四个外边距属性设置分别是： margin-top， margin-right， margin-bottom 和 margin-left 。指定的外边距允许为负数。

## 先上[栗子](http://js.jirengu.com/ligex/1/edit?html,css,output)

此次测试的方法是,通过设定margin-left的值从正值变为负值,来观察其对div大小和移动方向的改变

测试代码:
### HTML代码
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>负margin测试</title>
</head>
<body>
  <div class="container">
    <div class="test">test-test-test</div>
  </div>
</body>
</html>
```
### CSS代码
```
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.container{
  margin: 50px;
  border: 1px solid blue;
  
}
.test{
  margin: 20px;
  margin-left: 0px;
  border: 1px solid green;
}
```
测试代码很简单,由简单入手，正所谓删繁就简三秋树，领异标新二月花

### 第一步 设定margin-left: 0;
![margin0](/images/margin0.png)

如图所示,margin-left赋值为0px,覆盖了它的初始值10px。图中蓝色是container的border,绿色是content的border,可以看出content此时宽度为554px

### 第二步 设定margin-left: 30px;

![margin30](/images/margin30.png)

如图所示,可以看出content此时宽度为524px,对比上一步减少了30px,左边缘向右移动

## 第三步 设定margin-left: 50px;

![marign50](/images/margin50.png)

如图所示,可以看出content此时宽度为504px,对比上一步减少了20px,左边缘向右移动

此时不难看出,margin-left值增大时,test被向右挤压,宽度逐渐缩小

## 第四步 设定margin-left: 20px;

![marign20](/images/margin20.png)

如图所示,可以看出content此时宽度为534px,对比上一步增加了30px,左边缘向右移动

可以看出,当margin-left值减少时,test向左扩展,宽度逐渐增大

### 第五步 设定margin-left: -20px;

![marign-20](/images/margin-20.png)

如图所示,可以看出content此时宽度为574px,对比上一步增加了40px,左边缘向右移动

此时test的border已经突破了父元素的border

### 第六步 设定margin-left: -50px;

![marign-50](/images/margin-50.png)

如图所示,可以看出content此时宽度为574px,对比上一步增加了40px,左边缘向右移动

### 由此我们可以得出一个结论:margin-left变大，content的宽度变小，理解为挤扁了，margin-left变小，content的宽度变大，理解为外扩了


## 负margin在布局中的应用

### 不变的法则,举个[栗子](http://js.jirengu.com/nijuf/1/edit?html,css,output)

以下是代码:
### HTML代码
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>负margin的应用</title>
</head>
<body>
  <div class="container">
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
  </div>
</body>
</html>
```
### CSS代码
```
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.container{
  margin: 50px;
  width: 600px;
  outline: 2px solid green;
  display: flex;
}
.container>div{
  width: 120px;
  height: 120px;
  margin-right: 40px;
  border: 2px solid grey;
}
```

效果如下:

![flex-demo](/images/flex-demo.png)

这是在四栏布局中经常遇到的问题,设置margin-right时,最右侧会有间隙

此时就需要使用负margin解决,把代码改成如下:

![flex-code](/images/flex-code.png)

此是上面问题已完美解决,四栏布局完成,那增加的负margin究竟起了什么作用呢?

![margin作用div](/images/margin作用div.png)

如图所示,content的边框已经超出了container的边框

![margin作用.x](/images/margin作用.x.png)

如图所示,.x的宽度原本应该和container一样为600px,受负margin的影响,由上文可知margin-left变小，content的宽度变大.所以宽度变为640px

![margin作用.con](/images/margin作用.con.png)

如图所示,container的宽度依然为600px,此时由于display的默认样式hidden,所有.x突出的部分自然被隐藏了,四栏布局就实现了

这就是负margin在四栏布局中的作用








