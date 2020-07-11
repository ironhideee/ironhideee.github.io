---
title: 利用postMessage实现跨源通信
date: 2019-05-10
categories:
- 前端
tags:
- HTML5
---

一、业务背景：
利用OpenCMS构建一个包含公用页头页脚的容器，中间用iframe标签引入Angular页面（我也不想用iframe这种古董，但是解决方案目前只有这个最合适）

二、引发的问题：
众所周知iframe高度自适应问题不是简单一句height:100%就能解决的，尤其是当子页面不断调用接口，页面高度不固定时，需要实时计算子页面的高度。为了解决这个问题，最初我设想的是在父页面通过onload()事件去触发计算iframe高度的方法
``` javascript
    ifrme.height = iframe. contentWindow.document.documentElement.scrollHeight
```
那么问题来了，onload事件是页面加载完毕触发，可是页面加载完毕时接口调用仍在pending状态，所以并没有取到需要的数据填充到页面中，导致页面高度实际上是远低于实际高度的（因为页面加载了很多图片）。所以我们需要在接口返回之后去触发计算高度这个方法。

三、关于postMessage：
window.postMessage() 方法可以安全地实现跨源通信。
postMessage(data,origin)方法接受两个参数；data指要传递的数据，origin为字符串参数，指明目标窗口的源。

window.postMessage() 方法被调用时，会在所有页面脚本执行完毕之后（e.g., 在该方法之后设置的事件、之前设置的timeout 事件,etc.）向目标窗口派发一个 MessageEvent 消息。 该MessageEvent消息有四个属性需要注意： message 属性表示该message 的类型； data 属性为 window.postMessage 的第一个参数；origin 属性表示调用window.postMessage() 方法时调用页面的当前状态； source 属性记录调用 window.postMessage() 方法的窗口信息。

然后在父页面全局写入监听方法：
``` javascript
    window.addEventListener(e){ 
        if(e.data == data){ 
            getIframeHeight(); 
        } 
    }
```
这里需要对接受到到MessageEvent对象判断一下消息源，确保监听到到是正确的事件。

而在Angular代码每一处接口返回成功时，去调用通信方法
``` javascript
    window.parent && window.parent.postMessage(“the data we need”,”https://…….”)
```
这样就解决了动态刷新iframe页面高度的问题。

最后的最后，折腾了好久还是废弃了iframe的解决方案，因为在移动端出现了各种意想不到的问题，取而代之的是手写页面去读取远端可配置的数据源；总而言之，今后还是尽量避免iframe的使用，但是不妨碍积累一些使用经验！
