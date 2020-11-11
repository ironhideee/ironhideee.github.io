---
title: Angular ElementRef详解
date: 2020-11-11 09:43:27
categories:
- 前端
tags:
- Angular
---

# 一.为什么要用ElementRef
为了能够支持跨平台，Angular 通过抽象层封装了不同平台的差异，统一了 API 接口。如定义了抽象类 Renderer（已废弃，现在用Renderer2） 、抽象类 RootRenderer 等。此外还定义了以下引用类型：

            1. ElementRef；

            2. TemplateRef；

            3. ViewRef ；

            4. ComponentRef ；

            5. ViewContainerRef ；

# 二.ElementRef有什么作用
在应用层直接操作 DOM，就会造成应用层与渲染层之间强耦合，导致我们的应用无法运行在不同环境，如 web worker 中，因为在 web worker 环境中，是不能直接操作 DOM。有兴趣的读者，可以阅读一下 Web Workers 中支持的类和方法这篇文章。通过ElementRef 我们就可以封装不同平台下视图层中的 native 元素 (在浏览器环境中，native 元素通常是指 DOM 元素)，最后借助于 Angular 提供的强大的依赖注入特性，我们就可以轻松地访问到 native 元素。

# 三.如何使用ElementRef
ElementRef 的背后是一个可渲染的具体元素。在浏览器中，它通常是一个 DOM 元素。
nativeElement 是背后的原生元素，如果不支持直接访问原生元素，则为 null（比如：在 Web Worker 环境下运行此应用的时候）。
比如想要修改给一段富文本添加上颜色，就需要在渲染完成后给指定元素加上样式
1、引入相关模块
``` javascript
    import { Component, ElementRef, OnInit, Renderer2, ViewChild } from '@angular/core';
```

2、构造函数依赖注入
``` javascript
    constructor(
        private elementRef: ElementRef,
        private renderer: Renderer2
    ) { }
```

3、使用属性装饰符@ViewChild
``` javascript
    @ViewChild('divContent', {static: false}) greetDiv: ElementRef;
```
``` html
    <div [innerHTML]="hypeLink" #divContent></div>
```

* Angular8 在使用ViewChild时增加了静态标志static来定义何时需要解析@ViewChild()和@ContentChild();
对于静态查询（static：true），查询在视图创建后即可解决，但是在运行变更检测之前。不过，结果永远不会更新以反映视图的更改，例如ngIf和ngFor块的更改。
使用动态查询（static：false），分别在ngAfterViewInit()或ngAfterContentInit()之后对@ViewChild()和@ContentChild()进行查询解析。结果将根据视图的更改而更新，例如对ngIf和ngFor块的更改。

4、通过render2写入样式
``` javascript
    ngAfterViewInit() {
        this.renderer.setStyle(this.greetDiv.nativeElement, 'color', 'green');
    }
```

# 四.安全风险
ElementRef 允许直接访问 DOM 会导致应用在 XSS 攻击前面更加脆弱。要仔细评审对 ElementRef 的代码。
当需要直接访问 DOM 时，请把本 API 作为最后选择。优先使用 Angular 提供的模板和数据绑定机制。或者你还可以看看 Renderer2，它提供了可安全使用的 API —— 即使环境没有提供直接访问原生元素的功能。
