---
layout: post
title: Effective Java-读书笔记
categories: JVM
description: Effective Java-读书笔记
---

Effective Java 读书笔记

### 创建和销毁对象

1. 静态工厂方法代替构造器
```wiki
public static Singleton getInstance(){

}

优势:
1. 静态工厂有名称，能表达构造对象的含义
2. 返回原返回对象的任何子类对象(继承和多态)
3.
```
