---
title: JS事件冒泡及阻止
date: 2019-04-25
categories:
- 前端
tags:
- JavaScript
---

{% note success %}
事件冒泡 ：当一个元素接收到事件的时候 会把他接收到的事件传给自己的父级，一直到window 。（注意这里传递的仅仅是事件 并不传递所绑定的事件函数；所以如果父级没有绑定事件函数，就算传递了事件 也不会有什么表现 但事件确实传递了）
{% endnote %}

举个例子，先点击一下父级按钮再点击一下子级按钮，可以发现click事件也被冒泡传递到了它的父级

![](/image/event_bubbling/img1.png)

避免冒泡事件的方法是event.stopPropagation()方法，同上的例子

![](/image/event_bubbling/img2.png)

类似的问题，阻止默认事件的发生可以采用event.preventDefault()方法，例如a标签在点击后不发生跳转。
