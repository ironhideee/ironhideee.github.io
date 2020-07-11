---
title: Angular构造函数中修饰符的区别
date: 2020-01-09
categories:
- 前端
tags:
- Angular
---

  众所周知，Angular中引入了大量Java的语法思想，在日常开发中，常常定义变量的时候是不写修饰符的，对修饰符的理解也仅仅停留在Java阶段，如下：

    修饰符       当前类       同包          子类         其他包

    public       √           √            √             √
    protected    √           √            √             ×
    default      √           √            ×             ×
    private      √           ×            ×             ×

  在Angular中public、protected、private这几个修饰符也有着类似的意义

1、public：公有类型（默认），既可以在当前类里使用，也可以在类之外使用
``` javascript
    export class PublicService {
        constructor(){}
        public country: string = 'China';
    }
    //另一个组件component调用时
    constructor(public service: PublicService){
        console.log(this.service.country) //China
    }
```
2、protected： 保护类型，只有在当前类和子类中可以使用

3、private： 私有类型，只有在当前类中可以使用
``` javascript
    export class ParentViewComponent implements OnInit {
        constructor(){}
        public a:string = 'aaa';
        protected b:string = 'bbb';
        private c:string = 'ccc';
    }
    //  这里子类继承父类
    export class ChildViewComponent extends ParentViewComponent implements OnInit {
        constructor(){
            super()//执行父类的构造函数；必须至少执行一次，用来创建父类的this
        }

        ngOnInit(){
            console.log(this.a);//能访问
            console.log(this.b);//能访问
            console.log(this.c);//报错
        }
    }
```
4、既然详细了解了修饰符，typescript的关键字super()也是类似于java里super的思想
``` javascript
    class Parent {
        constructor() {
            console.log('parent')
        }
        get() {
            console.log("吃饭")
        }     
    }  
    class Child extends Parent {
        constructor() {
            console.log("child")
            super()
        }
        get() {
            console.log("不吃饭")
        }
        init() {
            super.get()
        }
    }  
    var children = new Child();
    children.init();
    //child
    //parent
    //吃饭
```
这个例子说明typescript的super.method()可以调用父类同名的方法；
实际上就是js中的Parent.prototype.method.call(this)
