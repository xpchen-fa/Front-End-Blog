---
title: 踩坑
date: 2018-08-20 14:41:30
updated: 2018-09-11 14:41:30
tags: javascript
description: 踩坑
---
#2018-09-11
> 前端阿里直传图片，当服务器上的地址是https时，是不能请求http;配置的sdk要修改https
```
//html
  <script src="https://gosspublic.alicdn.com/aliyun-oss-sdk-4.4.4.min.js"></script>
//alioss配置
const BUCKET = "thenewwork";
const REGION = "oss-cn-shanghai";
const option = {
    accessKeyId: accessKeyId,  //后端给
    accessKeySecret: accessKeySecret, //后端给
    stsToken: securityToken, //后端给
    secure: true,       //https时要加这句
    bucket: BUCKET,
    region: REGION
};
this.client = new OSS.Wrapper(option);

```

# 2018-08-20

> 如下写法：图片当作背景平铺，当url里面的图片路径有括号时，图片显示不出来
```
//HTML
<div class="preview-card" 
    :style="'background-image: url('+ basicInfo.cardOne +')'"
></div>

//CSS
    .preview-card{
        width: 120px;
        height: 182px;
        float: left;
        margin-bottom: 10px;
        margin-right: 10px;
        background-size: cover;
        background-position: center;
        border-radius: 4px;
        cursor: pointer;
    }
```
> 修改方面
```
<div class="preview-card" 
    :style="`background-image: url('${basicInfo.activityUrl}')`"
></div>
```
