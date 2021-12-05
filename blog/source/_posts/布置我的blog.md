---
title: 第二篇，布置我的blog
date: 2019-03-21
tags:
- 示例
header-img: https://user-images.githubusercontent.com/21190726/54736315-7855e580-4be5-11e9-8403-e5f1ac1a41a4.png
catalog: true
toc_nav_num: true
top: 10
---
# 如何布置我的Blog：
## 准确的找到开发位置
release分支的  blog/source/_post路径
## 我需要布置一个头部banner
在themes\huweihuang\_config.yml的文件中，有一个 【header-img】 选项。给予图片线上地址，即可完成banner替换。
可以看到在在themes\huweihuang\layout\_partial\header.ejs文件中有下列代码
>图1：
```html
.header.intro-header{
    <% if (is_home()) { %>
        background-image: url('<%= config["header-img"] %>') 
        /*config*/
    <%} else if (is_post()){%>
        background-image: url('<%= page["header-img"] || config["header-img"]  %>')
        /*post*/
    <%} else {%>
        background-image: url('<%= page["header-img"] || config["header-img"] %>')
        /*page*/
    <%} %>
}
```
找到对应的is_home判断，即banner图片控制代码段
## 如何置顶
https://blog.csdn.net/nineya_com/article/details/103394315
给标签增加top值,越大越考前
## 我需要布置我点进文章后的banner
如图1，我们需要找到设置 【page["header-img"]】的地方。其实page里面的配置就是我们这个MD文件里最上面的那些配置。
也可以自己通过设置_config.yml里面的 【header-img-article】来设置公共的文章内部header
## 布置完成这两个，我如果需要写文章在哪里？
找到blog/source/_posts文件夹。里面就是我需要写的文章了。
## 关于头部的4个TAB。
home就是主页。
About则应该是关于，在 blog\source\about下设置。
对应的tags archive在对应文件夹下设置即可。如果遇到了新问题我在更新本文
# 关于发布
## 本地打包流程：
直接npm run build 一键打包发布。就是这么舒服

## 图片放在哪里了：
在github的issue里面