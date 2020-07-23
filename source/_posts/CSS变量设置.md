---
title: CSS变量设置
date: 2020-07-23
categories:
  - 前端
tags:
  - CSS
---

  背景：之前对于大段文案的样式处理，通常采用富文本编辑器里设计颜色、字体等；如果需要根据运营人员配置的颜色来作为一个作用域内的非文本的，比如说分页的数字icon的底色、按钮的颜色、上传组件的颜色时，就需要用到CSS变量来进行样式控制。

  声明变量的时候，变量名前面要加两根连词线（--）(大小写敏感)
``` css
    body {
        --foo: #7F583F;
        --bar: #F7EFD2;
    }
```
  使用var()函数读取变量
``` css
    a {
        color: var(--foo);
        text-decoration-color: var(--bar);
    }
```
  Js操作CSS变量如下：
``` javascript
    // 设置变量
    document.body.style.setProperty('--primary', '#7F583F');
    // 读取变量
    document.body.style.getPropertyValue('--primary').trim();
    // '#7F583F'
    // 删除变量
    document.body.style.removeProperty('--primary');
```

  这样就可以做到通过后端返回的数据，来渲染非富文本场景的样式，例如渲染一个按钮的背景色；其中需要注意的是需要等dom节点根据数据渲染完成之后再进行js操作CSS变量的行为；可以采用setTimeout()或者在渲染完成的生命周期钩子里写。
``` javascript
    const mockData = [
        {text:text1,color:red},
        {text:text2,color:blue},
        {text:text3,color:green},
        {text:text4,color:gray},
    ]

    mockData.foreach((item,index)=>{
        document.getElementById(index+'').style.setProperty('--bg', item.color);
    })
```

``` html
    <div *ngfor="let item of mockData,let i =index">
        <div [id]="i" class="selectedColor">
            <div>{{item.text}}</div>
            <button>different color</button>
        </div>
    </div>
```

``` css
    .selectedColor button {
        background-color: var(--bg);
    }
```
  这样CSS变量就提供里js与css通信的一种途径；js可以将任意值存入样式表,不仅是样式元素，事件信息也可以存入样式表中。
``` javascript
    const docStyle = document.documentElement.style;
    document.addEventListener('mousemove',(e)=>{
        docStyle.setProperty('--mouse-x',e.clientX);
        docStyle.setProperty('--mouse-y',e.clientY);
    })
```

