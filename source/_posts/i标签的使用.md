---
title: i标签的使用
date: 2019-01-24
categories:
- 前端
tags:
- CSS
---

&emsp;&emsp;问题：使用i标签引入小图标如箭头、☑️ 等icon时不显示图标；

{% note success %}
i标签是行内元素，行内元素定义宽高是无效的，类比用a标签做button时定义宽高无效；需要设置display = block或者inline-block转化为块级元素才有效。
{% endnote %}

&emsp;&emsp;此外，对于行内元素，margin上下对其无效（左右有效）；而padding上下虽然有效，但是会覆盖上下的相邻元素而不是撑开，即不会影响其他行的位置；所以对于行内元素要避免使用margin-top、margin-bottom、padding-top、padding-bottom。
