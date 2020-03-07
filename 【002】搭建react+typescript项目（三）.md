# 正式开始做项目
## 路由
> 这两种是通常创建路由的方式；那么他们的区别在哪
```
import *** from 'react-router';

import *** from 'react-router-dom';
```
- react-router：提供了router的核心api。如Router、Route、Switch等，但没有提供有关dom操作进行路由跳转的api；
- react-router-dom：提供了BrowserRouter、Route、Link等api，可以通过dom操作触发事件控制路由。

由于react-router-dom里包含了react-router的依赖，因此我们在安装的时候只需要安装后者即可。
由于是typescript项目，所以需要按照@types/react-router-dom依赖这是typescript的声明文件
```
yarn add react-router-dom

yarn add --dev @types/react-router-dom
```
路由配置原理可以参考[ react-router中文文档](http://react-guide.github.io/react-router-cn/docs/guides/basics/Histories.html)

## 发现问题
1. @/pages 会提示ts找不到该模块是由于tsconfig.json中没有配置
```
"compilerOptions": {
    ···
    "baseUrl":"./",
    "paths":{
      "@/*":["src/*"]
    }
}
```