---
title: JavaScript原型
date: 2019-03-03
categories:
- 前端
tags:
- JavaScript
---

一、JavaScript 的原型：
1、如果所有对象都有私有字段[[prototype]]，就是对象都原型；
2、读一个属性，如果对象本身没有，则会继续访问对象的原型，直到原型为空或者找到为止。

二、ES6提供了三个直接访问操作原型的方法
1、Object.create 根据指定的原型创建新对象，原型可以是null;
2、Object.getPrototypeOf 获取一个对象的原型；
3、Object.setPrototypeOf 设置一个对象的原型。
