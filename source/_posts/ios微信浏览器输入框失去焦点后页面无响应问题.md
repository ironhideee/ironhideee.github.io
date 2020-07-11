---
title: ios微信浏览器输入框失去焦点后页面无响应问题
date: 2019-12-05
categories:
- 前端
tags:
- 兼容性
---

  问题描述：在ios微信浏览器上访问表单注册页面，当输入框输入文字完毕，微信键盘收回之后，页面出现失去焦点现象，点击仍和按钮或者输入框都没反应

  原因：微信键盘弹起后，若愿输入框被遮挡，页面整体会向上移动，当键盘收起时页面却未恢复，导致页面点击没反应

  解决方法：当输入框失去焦点时调用一下方法能完美解决问题
``` javascript
    function kickBack() {
        setTimeout(() => {
            window.scrollTo(0, document.body.scrollTop + 1);
            document.body.scrollTop >= 1 && window.scrollTo(0, document.body.scrollTop - 1);  
        }, 10)
    }
```