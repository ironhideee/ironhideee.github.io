---
title: CSS蒙层效果
date: 2018-12-01
categories:
- 前端
tags:
- CSS
---

&emsp;&emsp;今天在制作高保真视觉效果图的时候，遇到一个这样的需求：在一张图片的上层加了一层灰色的蒙板，使得整个图片达到变暗的效果。第一时间的想法就是现设置

    background: gray url(…) no-repeat;

然而这样达到的效果仅仅是图片覆盖背景色，并不能让他们重合。后来了解到了background-blend-mode 属性，他定义了背景层的混合模式（图片与颜色）。

    background-color: darkgrey; 
    background-blend-mode: multiply; 
    background-image: url(“img/img1.png”);

&emsp;&emsp;当设置属性值为multiply时，为正片叠底模式，如果是两张重叠的图片，将会重合显示而不是覆盖。这样暂时性的实现了所需要的视觉效果，然而我缺忽略了蒙板所实现的功能并不是让图片变暗，而是在hover的时候将蒙板撤去达到变亮的效果，所以最后的实现方式是在图片上层用绝对定位覆盖一个设置透明度的灰色区域，当hover时，该区域display:none;其中覆盖的模板应设为:

    position: absolute; 
    top: 0; 
    left: 0;

且宽高与图片保持一致。