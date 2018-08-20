---
title: 踩坑
date: 2018-08-20 14:41:30
updated: 2018-08-20 14:41:30
tags: javascript
description: 踩坑
---
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
