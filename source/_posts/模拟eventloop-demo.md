---
title: 模拟eventloop demo
date: 2019-10-03
categories:
- 前端
tags:
- Node
---

  Node的异步非阻塞I/O特性依赖于其内部的事件循环机制；
  下面简单模拟一下事件循环的实现：
``` javascript
    const eventloop = {
        queue : [],
        loop(){
            while(this.queue.length > 0){
                var callback = this.queue.shift();
                callback();
            }
            setTimeout(this.loop.bind(this),50);   //这里this指的是eventloop对象
        },
        add(callback){
            this.queue.push(callback);
        }
    }

    eventloop.loop();

    setTimeout(() => {
        eventloop.add(function(){
            console.log(1)
        })
    },500)

    setTimeout(() => {
        eventloop.add(function(){
            console.log(2)
        })
    },800)
```
  这里间隔500毫秒和800毫秒向循环里添加了两个事件，eventloop会不间断得去监听是否有新事件进入执行栈。

{% note success %}
事件循环：
每次循环过程称为Tick（即查看是否有但出来事件，将待处理事件存储在任务队列中），若有，则取出事件及回调函数放入stack由主线程执行。
{% endnote %}

{% note success %}
任务队列：
异步操作将相关回调添加到Task Queue中，异步操作由浏览器内核webcore执行。
{% endnote %}
