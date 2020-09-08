---
title: placeholder兼容性
date: 2020-09-08 21:08:25
categories:
  - 前端
tags:
  - CSS
---

```css
    input::-webkit-input-placeholder, textarea::-webkit-input-placeholder {
        /* WebKit browsers 适配谷歌 */
        color: #666;
        font-size: 16px;
    }

    input:-moz-placeholder, textarea:-moz-placeholder {
        /* Mozilla Firefox 4 to 18 */
        color: #666;
        font-size: 16px;
    }

    input::-moz-placeholder, textarea::-moz-placeholder {      
        /* Mozilla Firefox 19+ */
        color: #666;
        font-size: 16px;
    }

    input:-ms-input-placeholder, textarea:-ms-input-placeholder {
        /* Internet Explorer 10-11 */ 
        color: #666;
        font-size: 16px;
    }
```
