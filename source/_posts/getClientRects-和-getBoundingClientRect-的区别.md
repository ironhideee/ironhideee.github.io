---
title: getClientRects() 和 getBoundingClientRect()的区别
date: 2019-04-02
categories:
- 前端
tags:
- JavaScript
---

&emsp;&emsp;简单来说，getClientRects返回的其实是个数组，数组中有很多个类似getBoundingClientRect返回的对象(这样的对象称为TextRectangleList)。getBoundingClientRect返回的永远是最外框框的那个矩形区域相关的坐标偏移对象；而getClientRects是多行文字区域的坐标偏移集合。

例如：

![](/image/getClientRects/img1.jpg)

结果为：

![](/image/getClientRects/img2.jpg)
