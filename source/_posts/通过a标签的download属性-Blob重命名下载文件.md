---
title: 通过a标签的download属性+Blob重命名下载文件
date: 2019-07-06
categories:
- 前端
tags:
- JavaScript
---

背景：
从文件服务器下载文件资源时，通常存储的文件名称都经过二进制转译，导致下载时的文件名为失去语义的二进制字符串。对于定制化的文件服务器可以从后端进行下载重命名，再生成新的下载链接；而对于通用解决方案，可以由前端进行重命名操作。

解决方案：
常用的下载方式：
``` html
    <a [href]="download_url" target="_blank">
```
``` javascript
    //或者
    window.location.href = download_url;
```
这里采用H5 download属性 + Blob
``` html
    <a href="logo.gif" download="logo.gif">下载</a>
```
直接实现下载+重命名操作，但是有缺陷
1、不支持IE
2、跨域问题
如果需要下载的资源是跨域的，包括跨子域，在Chrome浏览器下，使用download属性是可以下载的;但是，并不能重置下载的文件的命名；而FireFox浏览器下，则download属性是无效的，也就是FireFox浏览器无论如何都不支持跨域资源的download属性下载。

那么为什么要结合Blob?
为了支持更多应用场景。
如果后端返回文件下载的Url，那么a标签的download可以支持；
如果是前端自动生成的动态文件，eg.利用PDF.js将当前页面截图以PDF文件格式进行下载时，或者想要下载页面的文本信息，可以借助Blob转换成二进制文件，作为元素的href属性，配合download属性，实现下载。

什么是Blob？

一个Blob对象就是一个包含有只读原始数据的类文件对象。Blob对象中的数据并不一定得是JavaScript中的原生形式。File接口基于Blob, 继承了Blob的功能，并且扩展支持了用户计算机上的本地文件。
创建Blob对象的方法有几种，可以调用Blob构造函数，还可以使用一个已有Blob对象上的slice()方法切出另一个Blob对象，还可以调用canvas对象上的toBlob方法。

表示二进制大对象，常用于文件上传下载，eg.使用Blob从服务器上GET某张图片
``` javascript
    var xhr = new XMLHttpRequest();    
    xhr.open("get", "pic.jpg", true);
    xhr.responseType = "blob";
    xhr.onload = function() {
        if (this.status == 200) {
            var blob = this.response;  // this.response也就是请求的返回就是Blob对象
            var img = document.createElement("img");
            img.src = window.URL.createObjectURL(blob);
            img.onload = function(e) {
                window.URL.revokeObjectURL(img.src); // 清除释放
            };
            document.getElementById('download').appendChild(img);    
        }
    }
    xhr.send();
```
代码：
``` javascript
    var funDownload = function (content, filename) {
        var url = document.createElement('a'); // 创建隐藏的可下载链接
        url.download = 'nameYouWant.txt'; //格式自定义
        url.style.display = 'none';
        var blob = new Blob([content]); // 字符内容转变成blob地址
        url.href = URL.createObjectURL(blob);
        document.body.appendChild(url); // 触发点击
        url.click();
        document.body.removeChild(url); // 然后移除
    };
```
结果：
方案最终未被采用，一方面组内规范要求尽量减少DOM操作，二是文件服务器直接面向外界的接口未提供直接面向文件资源的接口，需求废弃。
