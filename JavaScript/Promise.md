# Promise用法
- Javascript是一种单线程的语言，所有的代码必须按照所谓的“自上而下”的顺序来执行

应用实例：微信上传图片
```
//将图片上传到微信浏览器得到serveIds
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
```