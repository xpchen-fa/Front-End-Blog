# QRCode
## qrcode.js
> [github](https://github.com/davidshimjs/qrcodejs)

> [中文文档](http://www.runoob.com/w3cnote/javascript-qrcodejs-library.html)


## 个人实践
> 由于是在vue项目中使用可以直接采用npm

```
npm install --save qrcode
```

在项目中

```
<template>
    <div class="codeimg">
        <canvas id="canvas"></canvas>
    </div>
</template>


//js中
this.$nextTick(() => {
    //生成二维码
    const canvas = document.getElementById("canvas");

    QRCode.toCanvas(canvas, "123456", error => {
        if (error) {
            this.$logger.log("失败");
        }
    });
    this.$logger.log("成功", this.cardminlist);
})


//CSS中
#canvas{
    width: 3.66rem !important;
    height: 3.66rem !important;
}
```
