---
title: EXIF处理照片信息
date: 2019-10-14
categories:
- 前端
tags:
- Exif
---

  最近在处理人脸照片时遇到一个奇怪现象，iphone 自拍照片总是显示未识别到人脸，Android 机则未出现任何问题。经过一番排查测试发现，iphone 手机在自拍时生成的人脸照片尽管显示是正常的，但是当转成 base64 格式之后恢复，自动逆时针旋转 45 度。导致人脸识别时，旋转之后的照片是无法检测出人脸的，带来极为糟糕的使用体验；所以需要在图片传给后端之前做一次复位操作。

  解决方案就是读取照片中的方向信息，将旋转的照片进行复位；这里的读取信息使用开源的 exif-js，复位操作由于图片转 base64 格式一般使用 canvas 画布显示，所以旋转操作用 canvas 上下文的 rotate()或者 transform()皆可。

  下图展示了照片 exif 方向参数的八种位置:

![](/image/exif/orient_flag.gif)

  经过测试，苹果手机前置摄像头直立拍照对应 6 号位，摄像头在右横向拍照对应 1 号位(常规位置)，摄像头在左横向拍照对应 8 号位(需要转 180 度)
  示例代码如下：
``` javascript
    let imgObj = new Image();
    imgObj.src = imagesrc;
    setTimeout(){()=>{
        let orientation;                            //方向参数
        EXIF.getData(imgObj, function() {
            orientation = EXIF.getTag(this, 'Orientation');
        }
        let imgWidth = imgObj.width;                   //图片宽度
        let imgHeight = imgObj.height;                 //图片高度
        let canvas = document.createElement('canvas');
        let ctx = canvas.getContext('2d');
        let ratio = imgWidth/imgHeight;
        if(imgWidth>imgHeight && imgWidth>750){
            imgWidth = 750;
            imgHeight = Math.ceil(750/ratio);
        }else if(imgWidth<imgHeight && imgHeight>1350){
            imgWidth = Math.ceil(1350/ratio);
            imgHeight = 1350;
        }
        if(orientation == 6){
            canvas.width = imgHeight;
            canvas.height = imgWidth;
            ctx.rotate(90 * Math.PI / 180);
            ctx.drawImage(imgObj, 0, -imgHeight, imgWidth, imgHeight);
            //画布以(0,-imgHeight)为焦点顺时针旋转90度，此时宽为图片高，高为图片宽
        }else if(orientation == 3) {
            canvas.width = imgWidth;
            canvas.height = imgHeight;
            ctx.rotate(180 * Math.PI / 180);
            ctx.drawImage(imgObj, -imgWidth, -imgHeight, imgWidth, imgHeight);
        }else {
            canvas.width = imgWidth;
            canvas.height = imgHeight;
            ctx.drawImage(imgObj, 0, 0, imgWidth, imgHeight);
        }
        let database64 = canvas.toDataURL("image/jpeg",0.6)
    },500}
```
  p.s. exif-js 代码库存在一个类型校验缺陷，使用时需要修正一下，如下所示

![](/image/exif/github.png)
