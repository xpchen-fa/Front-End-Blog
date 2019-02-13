---
title: 图片上传
date: 2018-12-12 10:35:00
updated: 2018-12-12 10:35:00
tags: vue
description: js
---

## 前言
> 最近写了各种类型的关于图片上传的代码，决定做个总结好了。

## PART ONE

##### 微信图片上传
> 微信图片上传的主要流程

```
graph TB
调用微信接口:chooseImage,得到localIds-->2[拿到localIds以后调用微信uploadImage接口获得serveIds]
2-->将serveIds传给后端,后端去微信服务器取图片上传到自己的阿里云服务器上
```
> 前端就只有前两步，难点主要在于微信的uploadImage接口是异步的，这就导致我们无法一下子上传两张图片，最后使用递归一张张上传，成功实现方案如下：

```
data () {
    return {
        serveIds: [],
        localIds: []
    };
},
props: {
    // 最多的照片数量
    maxPictures: {
        type: Number,
        default: 3
    }
},
computed: {
    pictureSize () {
        return this.localIds.length;
    }
},
methods: {
    //调用本地相机
    onImagePicker: function () {
        wx.chooseImage({
            count: this.maxPictures - this.pictureSize, // 默认9
            sizeType: ["original", "compressed"], // 可以指定是原图还是压缩图，默认二者都有
            sourceType: ["album", "camera"], // 可以指定来源是相册还是相机，默认二者都有
            success: res => {
             // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
                this.upload(res.localIds);
            }
        });
    }
    
    //上传到微信服务器
    upload (localIds) {
        if (localIds && localIds[0]) {
            const localId = localIds.shift();
            wx.uploadImage({
                localId: localId,
                isShowProgressTips: 0,
                success: (res) => {
                    this.serveIds.push(res.serverId);
                    //图片显示的时候，再把locaId传入，图片显示说明图片上传成功
                    this.localIds.push(localId);
                    this.$emit("upload", this.serveIds);
                    this.$emit("input", this.localIds);
                    if (localIds.length) {
                        this.upload(localIds);
                    }
                }
            });
        }
    },
    deleteImage (data, index) {
        if (this.serveIds[index]) {
            this.localIds.splice(index, 1);
            this.serveIds.splice(index, 1);
            this.$emit("upload", this.serveIds);
            this.$emit("input", this.localIds);
        }
    },
}
```
##### 踩坑
> 开始的时候为了学习一下采用promise，结果发现很尴尬（比较菜）发现安卓没有问题，但是在苹果上传多张时依旧只能上传一张。坑（最后也没找到原因弃之）

```
methods: {
    onImagePicker: function () {
        wx.chooseImage({
            count: this.maxPictures - this.pictureSize, // 默认9
            sizeType: ["original", "compressed"], // 可以指定是原图还是压缩图，默认二者都有
            sourceType: ["album", "camera"], // 可以指定来源是相册还是相机，默认二者都有
            success: res => {
                this.localIds = this.localIds.concat(res.localIds); // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
                this.$emit("input", this.localIds);
            }
        });
    },
    upload(localId) {
        return new Promise((resolve, reject) => {
            wx.uploadImage({
                localId: localId,
                isShowProgressTips: 0,
                success: (res) => {
                    resolve(res.serverId);
                }
            });
        });
    },
    //保存图片，由父组件触发
    onSave() {
        this.serveIds = [];
        const localIds = this.localIds.slice(0);
        const uploadFun = [];
        localIds.forEach(item => {
            uploadFun.push(this.upload(item));
        });
        return Promise.all(uploadFun).then((serveIds) => {
            return serveIds;
    });
}

——————————————
// 父组件调用
this.$refs.ImagePicker.onSave().then((serverIds) => {
    const image = serverIds;
}).catch(() => {});


```
***
## PART TWO
> 使用input进行图片上传前端实现，主要参考官方的文档
```
<template>
    <input id="image-input" type="file" @change="onImagePicker" accept="image/*">
</template>

<script type="text/javascript">
import alioss from "./alioss.js";
export default {
    data () {
        return {
            ossUrls: [],
            imageUrl: []
        };
    },
    props: {
        // 最多的照片数量
        maxPictures: {
            type: Number,
            default: 999
        }
    },
    computed: {
        pictureSize () {
            return this.ossUrls.length;
        }
    },
    methods: {
        onImagePicker (data) {
            const file = data.target.files[0];
            this.$dispatch("fullscreenLoading", true);
            alioss.putObject(file).then((result) => {
                this.ossUrls.push(result.url);
                this.$emit("input", this.ossUrls);
            }).catch(err => {
                console.log("上传失败")
            });
        },
    }
}
</script>

————————index.html
// sdk
 <script src="https://gosspublic.alicdn.com/aliyun-oss-sdk-4.4.4.min.js"></script>


————————主要:  alioss.js阿里上传;参考官方文档
import OSS from "OSS"; //阿里依赖需要安装
import Vue from "vue";

const BUCKET = "***"
const REGION = "***"
const ACCESSKEYID = "***" //最好不要存在前端不安全
const ACCESSKEYSECRET = "**" //同上
const URL_PREFIX = "cxp";
export default {
    client: {},

    init: async function () {
        const option = {
            accessKeyId: ACCESSKEYID,
            accessKeySecret: ACCESSKEYSECRET,
            bucket: BUCKET,
            region: REGION,
            secure: true //如果域名是 https的必须要加 https请求
        };
        this.client = new OSS.Wrapper(option);
    },

    getRandomFileName (len) {
        len = len || 32;
        const $chars = "ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678";
        /** **默认去掉了容易混淆的字符oOLl,9gq,Vv,Uu,I1****/
        const maxPos = $chars.length;
        let pwd = "";
        for (let i = 0; i < len; i++) {
            pwd += $chars.charAt(Math.floor(Math.random() * maxPos));
        }
        return pwd;
    },

    async putObject (file) {
        await this.init();
        
        const fileName = URL_PREFIX + this.getRandomFileName(12) + "/" + file.name;
        
        return this.client.multipartUpload(fileName, file, {
            progress: function (p) {
                return function (done) {
                    // 小数点
                    const round = function (v, e) {
                        let t = 1;
                        for (; e > 0; t *= 10, e--) ;
                        for (; e < 0; t /= 10, e++) ;
                        return Math.round(v * t) / t;
                    };
                    done();
                };
            }
        }).then((res) => {;
            // 当图片过大时候，会自动分段上传
            if (res.url === undefined) {
                const url = res.res.requestUrls[0].split("?")[0];
                const obj = {
                    name: res.name,
                    url: url,
                    source_name: file.name
                };
                return obj;
            }
            res.source_name = file.name;
            return res;
        }).catch((err) => {
            /*eslint no-console: 0*/
            console.log("err:", err);
        });
    }
};

```

***
## PART THREE
> koa写图片上传接口参考[链接](https://www.cnblogs.com/beileixinqing/p/9152113.html)实现如下
```
const consola = require('consola')
const formidable = require('formidable')
const co = require('co')
const OSS = require('ali-oss')
const client = new OSS.Wrapper({
    accessKeyId: '***',
    accessKeySecret: '***',
    bucket: '***',
    region: '***'
})
const URL_PREFIX = 'cxp';

exports.upload = async ctx => {

    let getRandomFileName = function (len) {
        len = len || 32;
        const $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678'
        /** **默认去掉了容易混淆的字符oOLl,9gq,Vv,Uu,I1****/
        const maxPos = $chars.length
        let pwd = ''
        for (let i = 0; i < len; i++) {
            pwd += $chars.charAt(Math.floor(Math.random() * maxPos))
        }
        return pwd
    }

    let alioss_upLoad = function() {
        return new Promise(function(resolve, reject) {
            let form = new formidable.IncomingForm()
            form.parse(ctx.req, function(err, fields, files) {
                if (err) { reject(err) }
                // 文件名
                const newfile = URL_PREFIX + getRandomFileName(12) + '/' + files.file.name
                //ali-oss
                co(function*() {
                    var result = yield client.put(newfile, files.file.path)
                    resolve(result)
                }).catch(function(err) {
                    reject(err)   
                });
            });
        });
    };

    await alioss_upLoad().then((res)=>{
        ctx.body = {
            success: true,
            data: res.url
        }
    }).catch((err)=>{
        ctx.body = {
            success: false,
            message: '上传失败'
        }
    });
}

```



