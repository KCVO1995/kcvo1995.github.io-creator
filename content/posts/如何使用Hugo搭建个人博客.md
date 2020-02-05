---
title: "如何使用Hugo搭建个人博客"
date: 2019-12-10T15:24:33+08:00
draft: false
---


# Hugo是什么？
 Hugo是一个用Go语言编写的静态网站生成器，Hugo一般只需要几秒钟就能生成一个网站（每页少于1毫秒），被称为”世界上最快的网站构建框架“，是最热门的静态网站生成器之一，被广泛采用。

# 如何使用Hugo搭建个人博客
## Step1：安装Hugo
1. 下载[Hugo安装包](https://github.com/gohugoio/hugo/releases) 
   ![下在windos版本的安装包](/images/hugo安装包.png)

2. 下载到安装包，解压后把hugo.exe.放到x:\xxx\hugo
   
3. 添加环境变量：点击计算机图标-右键-属性-高级系统设置-系统变量-path![环境变量](/images/hugo-path.png) 
   
4.重启终端，运行hugo version。安装成功就能查看到版本号
![hugo_version](/images/hugo_version.png)

## Step2: 创建一个新的Hugo网页
1. 进入[Hugo官网](https://gohugo.io/)，点击Quick Start。
2. Step1操作前面已完成，直接操作Step2
3. 复制Step2的代码，在cmder上运行。注意这里要把”quickstart“改成“github用户名.github.io-creater!”(这样操作的好处是，上传到GitHub上后方便标识。)
![hugo-step2](/images/hugo-step2.png)

## Step3: 下载一个主题
1. 依次复制Step3的代码在cmder上运行
   ![hugo-step3](/images/hugo-step3.png)
2. 由于主题是国外资源，国内下载很慢，建议挂VPN下载

## Step4: 新建一篇文章
1. 复制Step4的代码在cmder上运行
    ![hugo-step4](/images/hugo-step4.png)
2. 打开新建的markdown文件
3. 将draft: true 改为false(draft为草稿意思，改为false后文章才会显示在blog上)
   ![hugo-step4-2](/images/hugo-step4-2.png)

## Step5: 开启Hugo服务器
1. 复制Step5的代码在cmder上运行
   ![hugo-step5](/images/hugo-step5.png)
3. 运行后不要关闭服务器(关闭后本地访问会失败)
4. 在浏览器中访问http://localhost:1313/或者127.0.0.1:1313
5. 此时将会看到你新建的blog

## Step6 自定义主题
1. 打开hugo网站根目录的config.toml
2. 可自定义一下代码，包括：语言、博客名
   ![hugo-step6](/images/hugo-step6.png)

## Step7 简历静态网站 
1. 复制Step7的代码在cmder上运行
   ![hugo-step7](/images/hugo-step7.png)
2. 运行后将会得到一个public的目录
3. 在hugo的更目录中创建名为“.gitignore”的文件
4. 编辑“.gitignore”,写入/public/(由于hugo根目录和public需要分别上传到GitHub，此操作可以防止子仓库的问题出现)
5. 在GitHub上新建一个仓库，命名为github用户名.github.io
6. 把public目录上传到新建的仓库中
7. 在你的git仓库里面Settings的GitHub Pages下你可以看到http://你的用户名.http://github.io的链接。
8. 打开此链接，就能看到你的Hugo已经被挂到网上了

# 大功告成
## 最后你还可这样做
1. 在你的GitHub上新建一个仓库，命名为github用户名.github.io-creator
2. 将Hugo根目录的文件上传到此仓库(注意：Hugo根目录上存储着原代码，Hugo根目录丢失，就无法写博客了)



