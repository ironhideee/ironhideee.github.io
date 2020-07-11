---
title: super关键字
date: 2019-10-03
categories:
- 前端
tags:
- JavaScript
---

  this和super的区别：
  this关键词指向函数所在的当前对象
  super指向的是当前对象的原型对象
``` javascript
    const EventEmitter  = require('events').EventEmitter;
    class Publishorder extends EventEmitter {
        constructor(){
            super();
            setInterval(()=>{
                this.emit('neworder', { price: Math.random() * 100 });
            },3000)
        }
    }
```
  上面写了一个每隔3秒发布一个随机价格订单的事件发射器的类Publishorder，其中super()指的是父类的构造函数，即Nodejs中的events.EventEmitter；如果不去调用父类构造函数，将无法在构造函数中使用this。即抛出这样一个错误：

    /Users/xuan/Desktop/Daily Demo/Daily-Demo/Node/eventemitter.js:6
        setInterval(()=>{
        ^
    ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
        at new Publishorder (/Users/xuan/Desktop/Daily Demo/Daily-Demo/Node/eventemitter.js:6:9)
        at Object.<anonymous> (/Users/xuan/Desktop/Daily Demo/Daily-Demo/Node/eventemitter.js:12:22)
        at Module._compile (internal/modules/cjs/loader.js:778:30)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:789:10)
        at Module.load (internal/modules/cjs/loader.js:653:32)
        at tryModuleLoad (internal/modules/cjs/loader.js:593:12)
        at Function.Module._load (internal/modules/cjs/loader.js:585:3)
        at Function.Module.runMain (internal/modules/cjs/loader.js:831:12)
        at startup (internal/bootstrap/node.js:283:19)
        at bootstrapNodeJSCore (internal/bootstrap/node.js:622:3)

  那么为什么javascript要有这样一个限制？因为有继承关系，子类创建了对象会用到父类的属性方法，所以父类也要初始化，不然继承父类而不用父类的属性和方法，那继承还有什么意义。
  所以，Class中的 super()，它在这里表示父类的构造函数，用来新建父类的 this 对象。
这里的super()相当于Parent.prototype.constructor.call(this)
