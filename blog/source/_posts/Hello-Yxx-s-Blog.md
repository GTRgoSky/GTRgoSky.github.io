---
title: 使用主题的示例
date: 2018-09-09 17:59:28
tags:
- 示例
catalog: true
toc_nav_num: true
---
# 一些ejs模板里的方法说明(持续更新)：
### 1、锚链接标题
toc.ejs 里的 toc是根据#作为锚链接的，例如 “一些ejs模板里的方法说明(持续更新)” 就会被收入到旁边的Contents里面
### 2、页面部分方法
https://hexo.io/zh-cn/docs/variables.html
### 3、Site settings 设置title 邮箱 头像等（_config.yml）...
SEOTitle: Wings Blog 设置标题
email: 314912145@qq.com 
description: "在dev部署"
keyword: ""
header-img: http://ozilwgpje.bkt.clouddn.com/scenery/building.jpg?imageslim 设置banner图
article-img: /img/user.jpg //设置自己的头像
404-img: /img/header_img/404.png
signature: false #show signature
signature-img: img/signature/BeanTechSign-white.png 
### 4、SNS settings 设置右侧链接icon
RSS: false
weibo_username:     Demonbaneen
zhihu_username:     Demonbane
github_username:    GTRgoSky
twitter_username:   Demonbane_x
facebook_username:  YenYuHsuan
linkedin_username:  huweihuang
### 5、使用该主题
theme: huweihuang(这个名字其实是找themes里面对应的主题)
### 6、 Sidebar settings 设置侧边秒数
```shell
sidebar: true                       # whether or not using Sidebar.
sidebar-about-description: "一个很菜的程序员"
widgets:
    featured-tags
    short-about
    recent-posts
    friends-blog
    archive
    category
```
### 7、设置右边栏友情链接
```shell
friends: [
    {
        title: "GitHub-Demo",
        href: "https://github.com/GTRgoSky/TestLOL"
    }
]
```
