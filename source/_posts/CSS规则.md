---
title: CSS规则
date: 2019-03-03
categories:
- 前端
tags:
- CSS
---

@规则
1.@charset
用于提示CSS文件使用的字符编码方式，必须出现在最前面

    @charset “utf-8”

2.@import
用于引入一个CSS文件，除了@charset规则不会被引入，其他文件的全部内容都能被引入

3.@media
对设备类型进行判断

4.@page
用于分页媒体访问网页时的表现设置

    @page {
        size:8.5in 11in;
        margin:10%;
        @top-left {
            content: “Hamlet”;
        }
        @top-right {
            content: “Page” counter(page);
        }
    }

5.@counte-style
产生一种数据，用于定义列表项的表现

6.@key-frames
产生一种数据，用于定义动画关键帧

7.@fontface
用于定义一种字体， icon font技术就是利用这个特性来实现的

8.support
检查环境的特性，与media类似

9.@namespace
用于跟XML命名空间配合的一个规则，表示内部的CSS选择器全都带上特定命名空间

10.viewport
用于设置视口的一些特性，不过兼容性目前不是很好，多数时候被html的meta代替

普通规则
分为两块：选择器、声明列表（分为属性和值（值的类型、函数））

CSS支持的计算型函数（仅举例）
1、calc()支持加减乘除四则运算
针对纬度进行计算时，允许不同单位混合运算

    width：calc(100%/3 - 21rem -21px);

2、max()

3、min()

4、clamp()
给一个值限定一个范围，超出范围外则使用范围的最大值或最小值

5、toggle()
在规则选中多个元素时生效，它会在几个值之间来回切换
例如想让一个列表项的样式圆点和方点间隔出现，可以使用

    ul { list-style-type: toggle( circle, square); }

6、attr()
允许CSS接受属性值（html的属性值）的控制

后期查阅整理后，按功能分为5个类别

图片

    filter
    blur()
    brightness()
    contrast()
    drop-shadow()
    grayscale()
    hue_rotate()
    invert()
    opacity()
    saturate()
    sepia()
    cross-fade()
    element()
    image-set()
    imagefunction()

图形绘制

    conic-gradient()
    linear-gradient()
    radial-gradient()
    repeating-linear-gradient()
    repeating-radial-gradient()
    shape()

布局

    calc()
    clamp()
    fit-content()
    max()
    min()
    minmax()
    repeat()

变形/动画

    transform
    matrix()
    matrix3d()
    perspective()
    rotate()
    rotate3d()
    rotateX()
    rotateY()
    rotateZ()
    scale()
    scale3d()
    scaleX()
    scaleY()
    scaleZ()
    skew()
    skewX()
    skewY()
    translate()
    translate3d()
    translateX()
    translateY()
    translateZ()

环境与元素

    var()
    env()
    attr()
