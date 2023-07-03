---
abbrlink: ''
categories:
- - debug
date: '2023-07-03T09:06:44.972361+08:00'
tags:
- debug
title: jsdelivr加速中遇到的问题
updated: 2023-7-3T9:5:42.900+8:0
---
最近发现网站访问的越来越慢了，今天尝试加加速。

网上查阅得到可以用CDN试试。

> https://cdn.jsdelivr.net/gh/用户名/仓库名/对应文件的路径

确实可以啊

曾经使用的在线图床管理工具也用的是类似的加速方式。

具体操作方式就是把所有静态资源进行替换。

# Package size exceeded the configured limit of 50 MB

访问图片发现这个问题，网上查阅的是上传时间太过久远，用@指定一下就行。

# 又拍云

听说这个也不错
