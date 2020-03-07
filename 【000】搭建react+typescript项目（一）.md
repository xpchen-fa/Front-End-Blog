# 手动搭建react + typeScript项目(一)
## 1. 初始化
```
yarn init
```

## 2. 安装依赖
react
```
yarn add react react-dom
```
React 类型库
```
yarn add --dev @types/react @types/react-dom
```
typeScript 和 ts-loader
```
yarn add --dev typescript ts-loader
```
Webpack
```
yarn add --dev webpack webpack-cli webpack-dev-server webpack-merge
```
Webpack插件
```
yarn add --dev source-map-loader html-webpack-plugin clean-webpack-plugin uglifyjs-webpack-plugin
```


## 3. webpack配置
- config文件夹，定义变量./config/index.js  区分开发和生产环境
    > 其中 env: require('./dev.env') =>为开发环境最后传入web中使用环境变量./config/dev.env.js 

    > env: require('./prod.env') =>为生产环境最后传入web中使用环境变量./config/dev.prod.js

    ```
    const path = require('path')

    module.exports = {
        dev: {
            env: require('./dev.env'),
            host: 'localhost',
            port: 8888,
            assetsRoot: path.resolve(__dirname, '../dist'),
            assetsPublicPath: '/',
        },
        prod: {
            env: require('./prod.env'),
            assetsRoot: path.resolve(__dirname, '../dist'),
            assetsPublicPath: '/',
        }
    };
    ```

    ```
    module.exports = {
        NODE_ENV: JSON.stringify('development')
    };
    ```

    ```
    module.exports = {
        NODE_ENV: JSON.stringify('production')
    };
    ```
    
- 基础webpack 配置 ./build/webpack.base.conf.js
    ```
    const webpack = require('webpack')
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    const path = require('path');

    const config = require('../config')

    const ENV = process.env.NODE_ENV || "development";
    const devMode = ENV === "development";

    module.exports = {
        mode: devMode ? "development" : "production",
        devtool: devMode ? "#source-map" : "inline-source-map",
        resolve: {
            extensions: ['.tsx', '.ts', '.js', '.json'],
            alias: {
                '@': path.resolve(__dirname, '../src')
            }
        },
        entry: './src/index.tsx',
        output: {
            path: config.prod.assetsRoot,
            publicPath: devMode ? config.dev.assetsPublicPath : config.prod.assetsPublicPath,
            filename: `js/[name]${devMode ? '' : '-bundle-[hash:8]'}.js`,// 文件名,不加hash,以方便调试时使用，生产环境下可以设置为 [name]-bundle-[hash:8].js
        },
        module: {
            rules: [
            {
                enforce: "pre",
                test: /\.js$/,
                loader: "source-map-loader"
            },
            {
                test: /\.(ts|tsx)$/,
                loader: "ts-loader",
            },
            ],
        },
        plugins: [
            new HtmlWebpackPlugin({
                title: 'React + Typescript demo',
                template: './index.html',
                inject: false,
            }),
            new webpack.DefinePlugin({
                'process.env': devMode ? config.dev.env : config.prod.env
            }),
        ],
    }
    ```
- 开发环境webpack 配置 ./build/webpack.dev.conf.js
    ```
    const webpack = require('webpack')
    const webpackMerge = require('webpack-merge')

    const baseConfig = require('./webpack.base.conf');
    const config = require('../config')

    const devConfig = {
        devServer: {
            historyApiFallback: true,// 在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
            disableHostCheck: true,// 关闭 Host 检查，同网段其他设备，可通过内网 IP 访问本机服务（需要配合 host: '0.0.0.0'）使用
            hot: true, // 模块热更新，取决于HotModuleReplacementPlugin
            host: config.dev.host,
            port: config.dev.port,
            open: true,
            inline: true,
            overlay: true,
            watchOptions: {
                ignored: /node_modules/,
            },
            // 请求代理服务
            // proxy: {
            //   '/api': {
            //     // 这里改为项目后端 API 接口 Host
            //     target: 'http://backend.api.host',
            //     // 支持跨域调用
            //     changeOrigin: true,
            //   }
            // }
        },
        plugins: [
            new webpack.HotModuleReplacementPlugin(),
            new webpack.NamedModulesPlugin()
        ],
    }
    module.exports = webpackMerge(devConfig, baseConfig);
    ```
- 开发环境webpack 配置 ./build/webpack.prod.conf.js
    ```
    const webpackMerge = require('webpack-merge');
    const CleanWebpackPlugin = require('clean-webpack-plugin').CleanWebpackPlugin; //"clean-webpack-plugin": "^3.0.0"
    const UglifyJsPlugin = require("uglifyjs-webpack-plugin");

    const baseConfig = require('./webpack.base.conf');

    const prodConfig = {
        plugins: [
            new UglifyJsPlugin({
                uglifyOptions: {
                    warnings: false
                },
                cache: true,
                parallel: true,
                sourceMap: true
            }),
            // 每次编译之前，清空上一次编译的文件
            new CleanWebpackPlugin()
        ],
    }
    module.exports = webpackMerge(prodConfig, baseConfig);
    ```

## 4. tsconfig.json文件配置
```
{
  "compilerOptions": {
    "allowSyntheticDefaultImports": true, 
    "jsx": "react",
    "lib": ["es6", "dom"],
    "module": "esnext",
    "moduleResolution": "node",
    "noImplicitAny": true,
    "rootDir": "src",
    "sourceMap": true,
    "strict": true,
    "target": "es5"
  },
  "exclude": [
    "node_modules",
    "build"
  ]
}
```
allowSyntheticDefaultImports: 允许合成默认导出
```
import * as React from 'react'
=>
import React from 'react'
```

## 5. 入口文件
- ./index.html
    ```
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title><%= htmlWebpackPlugin.options.title %></title>
        <% for (let css in htmlWebpackPlugin.files.css) { %>
        <link href="/<%=htmlWebpackPlugin.files.css[css] %>" rel="stylesheet">
        <% } %>
    </head>
    <body>
        <noscript>
        You need to enable JavaScript to run this app.
        </noscript>
        <div id="root"></div>
        <% for (let chunk in htmlWebpackPlugin.files.chunks) { %>
        <script type="text/javascript" src="<%=htmlWebpackPlugin.files.chunks[chunk].entry %>"></script>
        <% } %>
    </body>
    </html>
    ```
- ./src/index.tsx
    ```
    import React from 'react'
    import ReactDOM from 'react-dom'

    ReactDOM.render(
        <div>Hello React+typeScript</div>,
        document.getElementById('root'),
    )
    ```

## 6. package 启动脚本
```
{
  ...,
  "scripts": {
    "start": "webpack-dev-server --config ./build/webpack.dev.conf.js",
    "build": "webpack --config ./build/webpack.prod.conf.js"
  },
  ...
}
```

[参考](https://www.jianshu.com/p/cad0dd832514)