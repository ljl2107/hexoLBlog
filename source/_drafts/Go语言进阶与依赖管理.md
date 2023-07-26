---
abbrlink: Go语言进阶与依赖管理
categories: []
date: '2023-07-26T10:42:16.773947+08:00'
tags: []
title: Go语言进阶与依赖管理
updated: 2023-7-26T10:42:17.48+8:0
---
# go高并发

go语言为什么快？

因为go可以充分发挥多核的优势，高效运行。

协程：用户态，轻量级线程，栈KB级别。

线程：内核态，线程跑多个协程，栈MB级别。

go使用协程多。

## 开启协程

就是在函数前加一个go关键字。

## CSP（Communicating Sequential Processes）

* 通过通信共享内存。
* 通过共享内存实现通信。

**提倡通过通信共享内存而不是通过共享内存实现通信。**

## Channel

使用make关键字

* 无缓冲通道（同步通道）
* 有缓冲通道

## 并发安全Lock

并发时进行加锁。

![https://github.com/ljl2107/imageshack/blob/main/qexo/2023/7/image_7ce18108abb89bdaa6d36704ac976524.png](https://github.com/ljl2107/imageshack/blob/main/qexo/2023/7/image_7ce18108abb89bdaa6d36704ac976524.png)

## WaitGroup

不用sleep这种粗糙的方法。

实现并发任务的同步

三个方法

* add

* done
* wait

内部维护了一个计时器

我看和信号量有点像。

![https://github.com/ljl2107/imageshack/blob/main/qexo/2023/7/image_e15bb28916c695e220dde0a830153e16.png](https://github.com/ljl2107/imageshack/blob/main/qexo/2023/7/image_e15bb28916c695e220dde0a830153e16.png)

![https://github.com/ljl2107/imageshack/blob/main/qexo/2023/7/image_1ceb8210be4c29da27d457bab6e54122.png](https://github.com/ljl2107/imageshack/blob/main/qexo/2023/7/image_1ceb8210be4c29da27d457bab6e54122.png)

# 依赖管理

## 方案演进

GOPATH-》Go Vendor-》Go Module

## GOPATH

存在弊端，无法实现package的多版本控制。

## Go Vender

存在弊端，无法控制依赖的版本。更新项目又可能出现依赖冲突，导致编译出错。

## Go Module

* 通过go.mod文件管理依赖包版本
* 通过 go get/go mod 指令工具管理依赖包
