---
title: 【转】浅谈script标签中的async和defer
date: 2021-11-30 20:37:39
categories:
- 前端
tags:
- HTML
---


{% note success %}
script标签用于加载脚本与执行脚本，在前端开发中可以说是非常重要的标签了。
直接使用script脚本的话，html会按照顺序来加载并执行脚本，在脚本加载&执行的过程中，会阻塞后续的DOM渲染。
{% endnote %}

现在大家习惯于在页面中引用各种的第三方脚本，如果第三方服务商出现了一些小问题，比如延迟之类的，就会使得页面白屏。
好在script提供了两种方式来解决上述问题，async和defer，这两个属性使得script都不会阻塞DOM的渲染。
但既然会存在两个属性，那么就说明，这两个属性之间肯定是有差异的。

# defer
{% note success %}
如果script标签设置了该属性，则浏览器会异步的下载该文件并且不会影响到后续DOM的渲染；
如果有多个设置了defer的script标签存在，则会按照顺序执行所有的script；
defer脚本会在文档渲染完毕后，DOMContentLoaded事件调用前执行。
{% endnote %}

我们做了一个测试页面，页面中包含了两个script标签的加载，给他们都加上defer标识。
P.S. 为了更直观，我们给script1.js添加了1s的延迟，给script2.js添加了2s的延迟。
![](/image/script-defer-async/1.png)
下图是页面加载的过程中script脚本的输出顺序。
不难看出，虽然script1加载用时虽然比script2短，但因为defer的限制，所以Ta只能等前边的脚本执行完毕后才能执行。
![](/image/script-defer-async/2.png)
![](/image/script-defer-async/3.png)

# async
{% note success %}
async的设置，会使得script脚本异步的加载并在允许的情况下执行；
async的执行，并不会按着script在页面中的顺序来执行，而是谁先加载完谁执行。
{% endnote %}
![](/image/script-defer-async/4.png)
遂得到了如下的结果，页面加载时长上，并没有什么变化，毕竟都是异步加载的脚本。
但是我们可以看到一个小细节，DOMContentLoaded事件的触发并不受async脚本加载的影响，在脚本加载完之前，就已经触发了DOMContentLoaded。
![](/image/script-defer-async/5.png)
![](/image/script-defer-async/6.png)
![](/image/script-defer-async/7.png)
![](/image/script-defer-async/8.png)
我们接着修改测试页面。加载一个没有延迟的script脚本，使得脚本可以即时的加载完毕。
我们要测试一下，如果async脚本加载的足够快，是否会在DOMContentLoaded之前就执行（这个实验是基于对async的描述“在允许的情况下执行”的论证）。
同时为了保证测试的稳定性，我们在script脚本引入的后边添加了数千个空的div节点，用来延长文档的渲染时间。
![](/image/script-defer-async/9.png)
执行结果不出所料，如果给async一定的时间，是有可能在DOMContentLoaded事件之前就执行的。
![](/image/script-defer-async/10.png)
P.S. 从上图中左上角的火焰图中，我们也能看到，出现了多段的蓝色（更新：紫色的才是渲染，蓝色的是解析）文档渲染。以及下边Console的顺序。
说明的确，async的执行是加载完成就会去执行，而不像defer那样要等待所有的脚本加载完后按照顺序执行。

# 画几张图简要说明
{% note success %}
网上有了不少这种类似的图，但是基本都是拿一个script就举例的
咱们来画一下多个脚本加载时的甘特图
{% endnote %}
拿四个不同的颜色来标明各自代表的含义
![](/image/script-defer-async/11.png)

## 普通script
文档解析的过程中，如果遇到script脚本，就会停止页面的渲染进行下载（但是并不会影响后续的解析，解析和渲染是两码事）。
资源的下载是在解析过程中进行的，虽说script1脚本会很快的加载完毕，但是他前边的script2并没有加载&执行，所以他只能处于一个挂起的状态，等待script2执行完毕后再执行。
当这两个脚本都执行完毕后，才会继续渲染页面。
![](/image/script-defer-async/12.png)

## defer
文档解析时，遇到设置了defer的脚本，就会在后台进行下载，但是并不会阻止文档的渲染，当页面解析&渲染完毕后。
会等到所有的defer脚本加载完毕并按照顺序执行，执行完毕后会触发DOMContentLoaded事件。
![](/image/script-defer-async/13.png)

## async
async脚本会在加载完毕后执行。
async脚本的加载不计入DOMContentLoaded事件统计，也就是说下图两种情况都是有可能发生的
![](/image/script-defer-async/14.png)
![](/image/script-defer-async/15.png)

# 推荐的应用场景
## defer
defer是表明脚本在执行时不会影响页面的构造。也就是说，脚本会被延迟到整个页面都解析完毕后再运行。浏览器渲染页面，读取到包含defer属性的外部script标签时不会停止DOM渲染，而是异步下载，加载完整个页面再运行js。有多个defer的标签时，会按照顺序下载执行。
如果你的脚本代码依赖于页面中的DOM元素（文档是否渲染完毕），或者被其他脚本文件依赖。
例如：
1、评论框
2、代码语法高亮
3、polyfill.js
## async
async浏览器立即异步下载文件，不同于defer得是，下载完成会立即执行，此时会阻塞dom渲染，所以async的script最好不要操作dom。因为是下载完立即执行，不能保证多个加载时的先后顺序。
如果你的脚本并不关心页面中的DOM元素（文档是否渲染完毕），并且也不会产生其他脚本需要的数据。
例如：
1、百度统计

如果不太能确定的话，用defer总是会比async稳定。

参考资料: https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/script

原文章链接地址：https://www.cnblogs.com/jiasm/p/7683930.html
