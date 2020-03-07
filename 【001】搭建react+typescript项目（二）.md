# 手动搭建react + typeScript 项目（二）
> 在搭建项目（一）的基础上，学习一些常用的webpack目录结构模板，增加一些内容

## 一、安装依赖
- 用于npm、node版本检测插件
    ```
    yarn add --dev chalk semver shelljs
    ```
- 用于开发模式时终端漂亮的输出错误/警告
    ```
    yarn add --dev friendly-errors-webpack-plugin portfinder node-notifier
    ```
- 用于生产时终端漂亮的输出错误/警告
    ```
    yarn add --dev ora rimraf
    ```

## 二、添加 node、npm 版本检测，警告提示
    >  在./build/check-versions.js [注释参考](https://blog.csdn.net/qq_34645412/article/details/78818245)

    ```
    // 下面的插件是chalk插件，他的作用是在控制台中输出不同的颜色的字，
    // 大致这样用chalk.blue('Hello world')，这款插件只能改变命令行中的字体颜色
    const chalk = require('chalk');
    // 下面这个是semver插件，是用来对特定的版本号做判断的，
    // 比如 semver.gt('1.2.3','9.8.7') false 1.2.3版本比9.8.7版本低
    // semver.satisfies('1.2.3','1.x || >=2.5.0 || 5.0.0 - 7.2.3') true 1.2.3的版本符合后面的规则
    const semver = require('semver');
    // 下面这个插件是shelljs，作用是用来执行Unix系统命
    const shell = require('shelljs');
    const packageConfig = require('../package.json');

    function exec(cmd) {
        //脚本可以通过 child_process 模块新建子进程，从而执行 Unix 系统命令
        //下面这段代码实际就是把cmd这个参数传递的值转化成前后没有空格的字符串，也就是版本号
        //https://nodejs.org/api/child_process.html这是nodejs的子进程教程
        //require('child_process') node的模块，execSync(cmd)创建同步进程
        return require('child_process').execSync(cmd).toString().trim();
    }

    const versionRequirements = [{
        name: 'node',
        // 使用semver插件把版本信息转化成规定格式，也就是 ' =v1.2.3 ' -> '1.2.3' 这种功能
        currentVersion: semver.clean(process.version),
        // 这是规定的pakage.json中engines选项的node版本信息 "node":">= 4.0.0"
        versionRequirement: packageConfig.engines.node
    }];


    /*shell.which('npm')  返回：C:\PROGRAM FILES\NODEJS\NPM.CMD 返回绝对路径，否则返回null*/
    if (shell.which('npm')) {
        versionRequirements.push({
            name: 'npm',
            currentVersion: exec('npm --version'),
            versionRequirement: packageConfig.engines.npm
        });
    }

    module.exports = function () {
        const warnings = [];
        for (let i = 0; i < versionRequirements.length; i++) {
            const mod = versionRequirements[i];
            //上面这个判断就是如果版本号不符合package.json文件中指定的版本号，就执行下面的代码
            //把当前版本号用红色字体，符合要求的版本号用绿色字体，给用户提示具体合适的版本
            if (!semver.satisfies(mod.currentVersion, mod.versionRequirement)) {
            warnings.push(mod.name + ': '
                + chalk.red(mod.currentVersion) + ' should be '
                + chalk.green(mod.versionRequirement));
            }
        }

        // 提示用户更新版本
        if (warnings.length) {
            console.log('');
            console.log(chalk.yellow('To use this template, you must update following to modules:'));
            console.log();
            for (let i = 0; i < warnings.length; i++) {
            const warning = warnings[i];
            console.log('  ' + warning);
            }
            console.log();
            process.exit(1);
        }
    }; 
    ```

## 三、针对开发模式修改
- 添加 ./build/utils.js
    ```
    const packageConfig = require('../package.json');

    exports.createNotifierCallback = () => {
        const notifier = require('node-notifier');

        return (severity, errors) => {
            if (severity !== 'error') return;

            const error = errors[0];
            const filename = error.file && error.file.split('!').pop();

            notifier.notify({
            title: packageConfig.name,
            message: severity + ': ' + error.name,
            subtitle: filename || ''
            });
        };
    }; 
    ```

- 添加文件 ./build/devServer.js
    ```
    'use strict'
    require('./check-versions')();

    const path = require('path');
    const webpack = require('webpack');
    // friendly-errors-webpack-plugin插件，把webpack的错误和日志收集起来，漂亮的展示给用户，方便调试
    // 注意点，使用这个插件要遵守下点
    // 您需要关闭所有的错误日志记录，将webpack配置静默选项设置为true
    // 也就是遵循以下三点即可
    // 在使用webpack-dev-middleware插件，关于这个插件的解释在我的dev-sever-js配置文件中有解释，设置以下内容
    // app.use(require('webpack-dev-middleware')(compiler, {
    //   quiet: true, // 必须设置
    //   publicPath: config.output.publicPath,
    // }));
    // 使用webpack-dev-server时设置如下
    // {
    //   devServer: {
    //     quiet: true
    //   }
    // }
    // 使用webpack-hot-middleware中间件，关于这个插件的解释也在我的dev-server-js文章中
    // app.use(require('webpack-hot-middleware')(compiler, {
    //   log: () => {}
    // }));
    const FriendlyErrorsPlugin = require('friendly-errors-webpack-plugin');
    const portfinder = require('portfinder');
    const devWebpackConfig = require('./webpack.dev.conf');
    const utils = require('./utils');
    const config = require('../config');

    if (!process.env.NODE_ENV) {
    process.env.NODE_ENV = JSON.parse(config.dev.env.NODE_ENV);
    }

    module.exports = new Promise((resolve, reject) => {
        portfinder.basePort = process.env.PORT || config.dev.port;
        portfinder.getPort((err, port) => {
            if (err) {
            reject(err);
            } else {
            // publish the new Port, necessary for e2e tests
            process.env.PORT = port;
            // add port to devServer config
            devWebpackConfig.devServer.port = port;

            // Add FriendlyErrorsPlugin
            devWebpackConfig.plugins.push(new FriendlyErrorsPlugin({
                compilationSuccessInfo: {
                messages: [`Your application is running here: http://${devWebpackConfig.devServer.host}:${port}`],
                },
                onErrors: config.dev.notifyOnErrors
                ? utils.createNotifierCallback()
                : undefined
            }));

            resolve(devWebpackConfig);
            }
        });
    });    
    ```
    > ./build/webpack.dev.conf.js记得增加quiet: true
    ```
    devServer: {
        ···
        quiet: true // necessary for FriendlyErrorsPlugin
        ···
    }
    ```
- package 脚本修改
> 增加npm,node版本限制
```
{
  ...,
  "scripts": {
    "start": "webpack-dev-server --config build/devServer.js"
  },
  "engines": {
    "node": ">= 6.0.0",
    "npm": ">= 3.0.0"
  }
  ...
}
```

## 四、针对生产模式修改
- 添加文件 build/build.js
    ```
    'use strict'
    require('./check-versions')();

    const ora = require('ora');
    const rm = require('rimraf');
    const path = require('path');
    const chalk = require('chalk');
    const webpack = require('webpack');
    const config = require('../config');
    const webpackConfig = require('./webpack.prod.conf');

    const spinner = ora('building for production...');
    spinner.start();

    rm(config.prod.assetsRoot, err => {
        if (err) throw err;
        webpack(webpackConfig, (err, stats) => {
            spinner.stop();
            if (err) throw err;
            process.stdout.write(stats.toString({
            colors: true,
            modules: false,
            children: false, // If you are using ts-loader, setting this to true will make TypeScript errors show up during build.
            chunks: false,
            chunkModules: false
            }) + '\n\n');

            if (stats.hasErrors()) {
            console.log(chalk.red('  Build failed with errors.\n'));
            process.exit(1);
            }

            console.log(chalk.cyan('  Build complete.\n'));
            console.log(chalk.yellow(
            '  Tip: built files are meant to be served over an HTTP server.\n'
            + '  Opening index.html over file:// won\'t work.\n'
            ));
        });
    });
    ```
- package 脚本修改
> 增加npm,node版本限制
```
{
  ...,
  "scripts": {
    "build": "node build/build.js"
  },
  "engines": {
    "node": ">= 6.0.0",
    "npm": ">= 3.0.0"
  }
  ...
}
```

## 五、分环境coss-env
> 在大部分企业开发的时候是不止开发生产，两套环境，一般还有有开发，甚至demo环境的情况；
多环境部署的时候，不同环境的前端分别调用的后端接口不同。使用cross-env插件在脚本入口处
传入环境变量用以区分不同环境，更加便捷。
1. 安装依赖
    ```
    yarn add --dev cross-env
    ```
2. package脚本修改
    ```
    {
      ...,
      "scripts": {
        "start": "cross-env webpack-dev-server --config build/devServer.js", // NODE_ENV=development为默认可加可不加
        "test": "cross-env NODE_ENV=test webpack-dev-server --config build/devServer.js",    
        "build": " cross-env NODE_ENV=production node build/build.js"
      },
      ...
    }
    ```
3. config文件修改
    ```
    //  ./config/dev.env.js
    module.exports = {
        NODE_ENV: JSON.stringify(process.env.NODE_ENV)|| JSON.stringify('development')
    };

    // ./config/dev.prod.js
    module.exports = {
        NODE_ENV: JSON.stringify(process.env.NODE_ENV)|| JSON.stringify('production')
    };
    ```

## 六、css、less处理
### css、less
```
error  in ./src/assets/css/index.css

Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
> .container {
|     background-color: aquamarine
| }

 @ ./src/index.tsx 3:0-32
```
1. 安装依赖
    - css 添加style-loader、css-loader 
    ```
    yarn add --dev style-loader css-loader
    ```
    - 支持less 添加less、less-loader
    ```
    yarn add --dev less less-loader
    ```
2. 添加loader配置 文件：build/webpack.base.conf.js
    ```
    module.exports = {
        ···
        module: {
            rules: [
                ···
                {
                    test: /\.css$/,
                    use: ["style-loader", "css-loader"]
                },
                {
                    test: /\.less$/,
                    use: ["style-loader", "css-loader", "less-loader"]
                }
                ···
            ]
        }
        ···
    }
    ```
    > 注：
    - css-loader：读取并编译 js 中引入的 css 文件
    - style-loader： html 中创建一个 <style></style> 标签，里面是 css-loader 解析出的 css 样式。
    - webpack 配置中，style-loader 必须在 css-loader 之前，顺序不可变，即必须先由 css-loader 处理，再由 style-loader 处理。

### postcss-loader & autoprefixer
> autoprefixer 按照浏览器使用量决定需要添加哪些浏览器前缀，postcss-loader 按照 autoprefixer 的结果来添加浏览器前缀。
1. 安装依赖
    ```
    yarn add --dev postcss-loader autoprefixer
    ```
2. 配置
    - 方式一 文件build/webpack.base.conf.js
    ```
    module.exports = {
        ···
        module: {
            rules: [
                ···
                {
                    test: /\.css$/,
                    use: [
                        "style-loader", 
                        "css-loader",
                        {
                            loader: "postcss-loader",
                            options: {
                                plugins: [
                                    require("autoprefixer")()
                                ]
                            }
                        }
                    ]
                },
                {
                    test: /\.less$/,
                    use: [
                        "style-loader", 
                        "css-loader", 
                        "less-loader",
                        {
                            loader: "postcss-loader",
                            options: {
                                plugins: [
                                    require("autoprefixer")()
                                ]
                            }
                        }
                    ]
                }
                ···
            ]
        }
        ···
    }
    ```

    在package.json文件中添加
    ```
    "browserslist": [
        "defaults",
        "not ie < 11",
        "last 2 versions",
        "> 1%",
        "iOS 7",
        "last 3 iOS versions"
    ]
    ```

    >遇到的问题：最早的时候添加方式如下：
    
    ```
    {
        test: /\.less$/,
        use: [
            "style-loader", 
            "css-loader", 
            "less-loader",
            {
                loader: "postcss-loader",
                options: {
                    plugins: [
                        require("autoprefixer")(
                            "browsers": [
                            "defaults",
                            "not ie < 11",
                            "last 2 versions",
                            "> 1%",
                            "iOS 7",
                            "last 3 iOS versions"
                        ])
                    ]
                }
            }
        ]
    }

    //如果不添加browsers则不生效
    //如果按照上面方式添加的话会报警告
    Replace Autoprefixer browsers option to Browserslist config.
    Use browserslist key in package.json or .browserslistrc file.

    Using browsers option cause some error. Browserslist config
    can be used for Babel, Autoprefixer, postcss-normalize and other tools.

    If you really need to use option, rename it to overrideBrowserslist.

    Learn more at:
    https://github.com/browserslist/browserslist#readme
    https://twitter.com/browserslist
    ```

    - 方式二 在项目根目录下新建 postcss.config.js
    ```
    module.exports = {
        plugins: [
            require('autoprefixer')()
        ]
    }
    ```
    文件：build/webpack.base.conf.js
    ```
    module.exports = {
        ···
        module: {
            rules: [
                ···
                {
                    test: /\.css$/,
                    use: ["style-loader", "css-loader","postcss-loader"]
                },
                {
                    test: /\.less$/,
                    use: ["style-loader", "css-loader", "less-loader","postcss-loader"]
                }
                ···
            ]
        }
        ···
    }
    ```
    > 总结：由于方式二需要新增文件，推荐选择方式一

## 七、图片和字体文件处理
> url-loader内部需要用到file-loader,因此我们需要安装url-loader和file-loader。

> url-loader工作分两种情况：
    1. 文件大小小于limit参数，url-loader将会把文件转为DataURL;
    2. 文件大小大于limit，url-loader会调用file-loader进行处理，参数也会直接传给file-loader。
1. 配置
    ```
    yarn add --dev file-loader url-loader
    ```
2. 添加loader配置 文件：build/webpack.base.conf.js
    ```
    module.exports = {
        ···
        module: {
            rules: [
                ···
                {
                    test: /\.(svg|png|jpe?g|gif|mp4|webm)(\?\S*)?$/,
                    loader: "url-loader?limit=8192&name=img/[name].[hash:7].[ext]"
                },{
                    test: /\.(eot|woff|woff2|ttf)(\?\S*)?$/,
                    loader: "url-loader?limit=8192&name=fonts/[name].[hash:7].[ext]"
                }
                ···
            ]
        }
        ···
    }
    ```

## 八、ESlint
> 我们通常使用lint工具来检查代码不规范的地方
1. 安装依赖
```
yarn add --dev eslint eslint-loader
yarn add --dev eslint-plugin-react @typescript-eslint/eslint-plugin @typescript-eslint/parser
```
- eslint: ESLint的核心代码
- @typescript-eslint/parser：ESLint的解析器，用于解析typescript，从而检查和规范Typescript代码
- @typescript-eslint/eslint-plugin：这是一个ESLint插件，包含了各类定义好的检测Typescript代码的规范
- 为了检测和规范React代码的书写必须安装插件eslint-plugin-react

2. 添加文件在根目录下新建.eslintrc.js文件
    - 在ts项目中必须执行解析器为@typescript-eslint/parser，才能正确的检测和规范TS代码
    - env环境变量配置，形如console属性只有在browser环境下才会存在，如果没有设置支持browser,那么可能报console is undefined的错误。
```
module.exports = {

  parser:  '@typescript-eslint/parser',
  extends: [
      'plugin:react/recommended',
      'plugin:@typescript-eslint/recommended'
  ],                              //使用推荐的React代码检测规范
  plugins: ['@typescript-eslint'],
  env:{                         
      browser: true,
      node: true,
  },
  settings: {             //自动发现React的版本，从而进行规范react代码
      "react": {
          "pragma": "React",
          "version": "detect"
      }
  }, 
  parserOptions: {        //指定ESLint可以解析JSX语法
      "ecmaVersion": 2019,
      "sourceType": 'module',
      "ecmaFeatures":{
          jsx:true
      }
  },
  rules: {
      //自定义React代码编码规范
  }
}
```
3. 还需要配置webpack，才能在开发时启用eslint
build/webpack.base.conf.js
```
module.exports = {
    ···
    module: {
        rules: [
            ···
            {
                enforce: 'pre',
                test: /\.tsx?$/,
                exclude: /node_modules/,
                include: path.resolve(__dirname, '../src'),
                loader: 'eslint-loader',
                options: {
                    emitWarning: true, // 这个配置需要打开，才能在控制台输出warning信息
                    emitError: true, // 这个配置需要打开，才能在控制台输出error信息
                    fix: true // 是否自动修复，如果是，每次保存时会自动修复可以修复的部分
                }
            }
            ···
        ]
    }
    ···
}
```
4. 我们可能希望检查或不检查某些特定的文件，可以在根目录新建.eslintignore
