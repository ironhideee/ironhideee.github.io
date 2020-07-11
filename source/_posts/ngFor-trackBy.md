---
title: ngFor-trackBy
date: 2019-09-07
categories:
- 前端
tags:
- Angular 
---

  今天在开发自定义表单功能时遇到这么一个问题，场景大致是通过循环生成所需的n个输入框，彼此之间保持独立。

先看这段代码存在到问题：
``` html
    <div *ngFor="let child of item.option; let k = index">
        <input nz-input class="option_input" [(ngModel)]="child">
        <i nz-icon [nzType]="'close'" class="btnIcon closeBtn" (click)="delItem(i,k)" *ngIf='item.option.length>1'></i>
        <i nz-icon [nzType]="'plus'" class="btnIcon plusBtn" (click)="addItem(i,k)"></i>
    </div>
```
  现象是新增输入框时正常工作，但在删除时，尽管ts代码获取到了所需要删除的index并在数据层更新了列表数组，但在页面渲染时删除的却是数组的最后一项。这时猜测视图上输入框双向绑定的属性与数组里到元素并没有一一对应上，才导致渲染的错误；于是修改为以下代码：
``` html
    <div *ngFor="let child of item.option; let k = index">
        <input nz-input class="option_input" [(ngModel)]="item.option[k]">
        <i nz-icon [nzType]="'close'" class="btnIcon closeBtn" (click)="delItem(i,k)" *ngIf='item.option.length>1'></i>
        <i nz-icon [nzType]="'plus'" class="btnIcon plusBtn" (click)="addItem(i,k)"></i>
    </div>
```
  但问题并没有解决，删除索引错乱的问题解决了，但对于每个input框都存在按键之后立即失去焦点的问题，想了一下还是视图与模型并没有对应的问题，于是翻文档找到了如下解决方案：
``` html
    <div *ngFor="let child of item.option; trackBy: trackByIndex; let k = index">
        <input nz-input class="option_input" [(ngModel)]="item.option[k]">
        <i nz-icon [nzType]="'close'" class="btnIcon closeBtn" (click)="delItem(i,k)" *ngIf='item.option.length>1'></i>
        <i nz-icon [nzType]="'plus'" class="btnIcon plusBtn" (click)="addItem(i,k)"></i>
    </div>
```
  以上问题全部解决，先解释一下一开始两种情况出现的原因。只删除最后一项的场景，通过打印集合数据可以知道页面输入值根据[(ngModel)]=”child”并未同步给数组，数组中仍然是初始化的空字符串，所以当删除一项时，视图只知道循环减一并不知道删除哪项。而对于[(ngModel)]=”item.option[k]”，视图与集合同步，只删除最后一项的问题解决，但存在另一个问题。

  我们知道对于双向绑定的属性可以做到同步更新视图，但是对于一个集合（这里是一个数组），视图无法去跟踪集合里每一个项，也就是当集合数据发生改变时，视图并不知道哪一项多了或哪一项少了，Angular所做的只能是将集合里的项全部移除再重新添加，涉及到的页面重新渲染，造成大量不必要到DOM操作，引入trackBy的目的是优化渲染性能，视图根据trackBy返回的唯一标示去重新绘制仅发生改变的项。
``` js
    const trackByIdentity = (index: number, item: any) => item;
```
  trackBy函数需要两个参数，第一个是当前项的index，第二个是当前项，并返回一个唯一的标识,相当于同步返回集合内发生了什么变化给视图用于重新绘制页面。
