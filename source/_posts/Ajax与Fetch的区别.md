---
title: Ajax与Fetch的区别
date: 2020-05-23
categories:
- 前端
tags:
- 数据请求方式
---

fetch是ajax的替代品；基于标准Promise实现支持async/await，可读性更高，避免了ajax回调地狱的痛点。

Ajax的核心是XMLHttpRequest对象，浏览器正是通过XMLHttpRequest对象进行http通信；
``` javascript
    //1.创建Ajax对象
    if(window.XMLHttpRequest){
        let oAjax=new XMLHttpRequest();
    }else{
        let oAjax=new ActiveXObject("Microsoft.XMLHTTP"); //兼容老版本IE
    }
    //2.连接服务器（打开和服务器的连接）
    oAjax.open('GET', url, true);
    //3.发送
    oAjax.send();
    //4.接收
    oAjax.onreadystatechange=function (){
    if(oAjax.readyState==4){
        console.log(oAjax)
        if(oAjax.status==200){
            //alert('成功了：'+oAjax.responseText);
            console.log(oAjax)
            fnSucc(oAjax.responseText);
        }else{
            //alert('失败了');
            if(fnFaild){
                fnFaild();
            }
        }
        }
    };
```
onreadystatechange 是一个事件句柄。它的值 (state_Change) 是一个函数的名称，当 XMLHttpRequest 对象的状态发生改变时，会触发此函数。状态从 0 (uninitialized) 到 4 (complete) 进行变化。仅在状态为 4 时，我们才执行代码。
``` javascript
    fetch('http://localhost:3000/api/data', {
        method:'GET',
        headers: { // 请求头（可以是Headers对象，也可是JSON对象）
            'Content-Type': 'application/json',
            'Accept': 'application/json'
        },
        mode:'cors',// 允许发送跨域请求
        credentials: 'include'
    }).then(function(response){
        //打印返回的json数据
        response.json().then(function(data){
            console.log(data);
        })
    }).catch(function(e){
        console.log('error: ' + e.toString());
    })
```
1）fetch只对网络请求报错，对400，500都当做成功的请求，需要封装去处理
2）fetch默认不会带cookie，需要添加配置项
3）fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
4）fetch没有办法原生监测请求的进度，而XHR可以

ajax和fetch在调用本地文件的时候都存在跨域的问题；ajax可以通过关闭cors限制的chrome实现；fetch却不行，只能用express起本地服务来调用。
