---
title: CSRF攻击防范
date: 2019-07-24
categories:
- 安全
tags:
- 安全
---

{% note success %}
CSRF（Cross-site request forgery）:跨站请求伪造，是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。
{% endnote %}

CSRF原理：
CSRF攻击思想如下：

![](/image/csrf/1.jpg)

引用自https://www.cnblogs.com/hyddd/archive/2009/04/09/1432744.html

CSRF攻击成功的两个必要条件
1、登录受信任的网站A，且本地存储了Cookie；
2、A站点Cookie生效的情况下，访问危险站点B。

防范方案：
在HTTP的 Request Header自定义属性进行验证
CSRF的核心是利用浏览器缓存的Cookie，通过图片、广告等各种手段诱导用户去点击一个链接。比如某银行的转账是通过用户访问 http://bank.test/test?page=10&userID=101&money=10000 页面完成，用户必须先登录bank.test，然后通过点击页面上的按钮来触发转账事件；这里只是举个例子，支付大多还是通过form表单的形式提交。
解决方案是当客户端请求服务器时（一般是用户登录的时候），服务器随机生成一个token写入返回的Cookie中，浏览器拿到token之后写入请求头中作为一个自定义参数；这样服务器在接受请求时就会先去校验CSRF-token，如果没有或者不正确都视为伪造请求返回500。

const headers = new HttpHeaders().set("X-HD-CSRF", "CSRF-Token"); 

![](/image/csrf/2.jpg)