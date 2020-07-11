---
title: 跳出forEach循环
date: 2019-09-02
categories:
- 前端
tags:
- JavaScript
---

  在循环遍历数组时，for循环可以通过break或者continue进行终止跳出操作；但是箭头函数的普及更多得使用forEach去做循环遍历，这时候想要中途跳出该怎么办呢？

构造场景,将数组中第一个值为5的元素改为10:
``` javascript
    const arr = [0,1,2,3,4,5,6,7,8,9];
    for (i=0;i<arr.length;i++){
        if(arr[i] == 5){
            arr[i] = 10;
            break;
        }
        console.log(arr[i]);
    }
    //0,1,2,3,4,10
```
用forEach改写
方案一：
``` javascript
    arr.forEach(item => {
        if(item == 5){
            item = 10;
            break;
        }
        console.log(item);
    })
    //Uncaught SyntaxError: Illegal break statement
```
所以原生forEach()不支持break操作；

方案二：
``` javascript
    arr.forEach(item => {
        if(item == 5){
            item = 10;
            return false;
        }
        console.log(item);
    })
    //0,1,2,3,4,6,7,8,9
```
return false起作用了，但仅仅是终止本次继续执行，后续的循环继续执行；

方案三：
``` javascript
    try {
        const arr = [0,1,2,3,4,5,6,7,8,9];
        arr.forEach(item => {
            if(item == 5){
                item = 10;
                throw new Error("EndIterative");
            }
            console.log(item);
        })
    }catch(e){
        if(e.message!="EndIterative") throw e;
    }
    //0,1,2,3,4
```
forEach()无法通过正常流程终止，所以可以通过抛出异常的方式实现终止循环的目的；

方案四：
``` javascript
    arr.every(item => {
        if(item == 5){
            item = 10;
            return false;
        }
        console.log(item);
        return true;
    })
    //0,1,2,3,4,false

    arr.some(item => {
        if(item == 5){
            item = 10;
            return true;
        }
        console.log(item);
        return false;
    })
    //0,1,2,3,4,true
```  
可以对于some(),return true时会终止循环，对于every()，return false时会种植循环。
