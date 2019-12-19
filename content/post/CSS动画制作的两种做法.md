---
title: "CSS动画制作的两种做法"
date: 2019-12-18T15:23:26+08:00
draft: false
---
# 我们来做一颗跳动的心吧
## 首先我们需要一颗心
百度搜索"心"![heart](/images/heart.png)

......这个也太可怕了吧,不适合儿童观众,我们还是自己用CSS画一个吧.

心怎么画?我们学的块级元素都是方形的,算加上border-radius也顶多只能画个 球

好吧!发挥我们的想象力,我们可以把它分解成两圆一方
![丑](/images/ugly.png)

代码如下:

![code](/images/code.png)

## 用transform方法制作动画
我们需要做的动画让心放大-缩小-放大-缩小,以达到跳动的效果

在原有代码中增加tranform的transition(动画过渡)和scale(缩放),代码如下:

![code](/images/code1.png)

transform属性中

* `transition: all 1s;`动画播放时间1s
* `transform: scale(1.5);`元素放大1.5倍 

动画效果:

![demo](/images/demo.gif)

## 用animation方法制作动画
在原有代码中增加animation和@keyframes heart(窗口名称),代码如下:

![code](/images/code2.png)

animation属性中:
* `animation:.5s heart infinite alternate-reverse;`动画播放时间0.5s,目标窗口名称heart,infinite为无限播放,alternate-reverse为反向开始循环播放
* 
  ```
  @keyframes heart {
    0%{transform: scale(1);}
    100%{transform: scale(1.5);}
  }
在0%时刻缩放比为1,在100%时刻缩放比1.5

动画效果: 

![demo1](/images/demo1.gif)

## 用animation方法制作多个时间点动画

多个时间点动画表示,在多个时刻进行不同的变化;

在上一步的代码中修改@keyframes heart,代码如下:

```
@keyframes heart {
  0%{
    transform: scale(1);
  }
  50%{
    transform: scale(.5);
  }
  100%{
    transform: scale(1.5);
  }
}
```
在0%时刻缩放比为1,在50%时刻缩放比0.5,在100%时刻缩放比1.5

动画效果:

![demo1](/images/demo2.gif)


## 总结:
* transform和animation都可以用于制作CSS动画,animation在制作多个时间点动画会更加方便.
* transform和animation还有很多用法,请在MDN中进行学习.
* 虽CSS属性虽然简单,但是要用好靠的是想象力


















