---
title: React项目整合typescript
date: 2018-08-12 01:44:28
categories: 
            - javascript
            - React
            - npm
tags: 
     -- 易忘重要知识点
     -- npm
     -- yarn
     -- create-react-app
     -- typescript
     -- node-sass
---

- [项目捣鼓第一坑（React + typescript）](#%E9%A1%B9%E7%9B%AE%E6%8D%A3%E9%BC%93%E7%AC%AC%E4%B8%80%E5%9D%91%EF%BC%88react--typescript%EF%BC%89)
    - [React项目配置起手](#react%E9%A1%B9%E7%9B%AE%E9%85%8D%E7%BD%AE%E8%B5%B7%E6%89%8B)
        - [安装create-react-app](#%E5%AE%89%E8%A3%85create-react-app)
        - [窥探react-scripts](#%E7%AA%A5%E6%8E%A2react-scripts)
        - [配置typescript](#%E9%85%8D%E7%BD%AEtypescript)
        - [配置node-sass](#%E9%85%8D%E7%BD%AEnode-sass)
    - [将项目跑起来](#%E5%B0%86%E9%A1%B9%E7%9B%AE%E8%B7%91%E8%B5%B7%E6%9D%A5)
        - [定义处理sass的`npm scripts`](#%E5%AE%9A%E4%B9%89%E5%A4%84%E7%90%86sass%E7%9A%84npm-scripts)
        - [将`npm srcipts`流程打通](#%E5%B0%86npm-srcipts%E6%B5%81%E7%A8%8B%E6%89%93%E9%80%9A)

<!-- more -->

# 项目捣鼓第一坑（React + typescript）

## React项目配置起手

项目起手参考了[TypeScript-React-Starter](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter)

### 安装create-react-app

1. 全局安装`create-react-app`

    ``` bash
    npm install -g create-react-app

    #yarn add -g create-react-app
    ```

   使用npm/cnpm/yarn都可以。此处不再赘述

2. 创建新的项目

    ```bash
    # 默认方式 使用react-scripts
    create-react-app foo

    # 自定义 scripts 包方式
    create-react-app foo --scripts-version=react-scripts-ts
    ```
    create-react-app简称`CRA`. 执行上述的bash操作后，会生成项目文件夹。但是没有webpack的配置文件。这里就要介绍react-scripts了。

    >This package includes scripts and configuration used by Create React App.

    就是说这是给CRA使用的包含一系列的scripts和配置的包。无需开发者关心ES6,JSX...的语法支持、devServer启动、js引入css文件、生成打包文件等等一系列的繁琐配置

    ---

### 窥探react-scripts

项目的`package.json`文件

```JSON
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
}
```

定义`npm scripts`的`start`处执行了`react-scripts start`。此时我们将视角转向`node_modules`下的`react-scripts`

```JSON
    // react-scripts/package.json
    "bin": {
    "react-scripts-ts": "./bin/react-scripts-ts.js"
    },
    "dependencies": {
        "autoprefixer": "7.1.6",
        "babel-jest": "^22.1.0",
        "babel-loader": "^7.1.2",
        "babel-preset-react-app": "^3.1.1",
        ...
    },
```

`bin` 表示react-scripts的可执行文件指向了`./bin/react-scripts.js`。即`环境变量Path`中安装了`react-scripts`，执行`react-scripts`时便指向`react-scripts.js`。后面的dependencies都是所需配置的依赖。

![/bin/react-scripts.js](/images/react_ts_script.png)

看到这里就恍然大悟了。这里`start build`等命令都指向了`scripts/start.js`等文件。`start.js` `build.js`里面就是webpack、devServer等开发环境和生产环境的配置

### 配置typescript

注意我们使用create-react-app使用自定义包安装react-scripts-ts。
即`create-react-app foo --scripts-version=react-scripts-ts`

>react-scripts-ts is a set of adjustments to take the standard create-react-app project pipeline and bring TypeScript into the mix.

配置完成后目录结构如下

```sh
.
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
├── src
│   ├── App.css
│   ├── App.test.tsx
│   ├── App.tsx
│   ├── index.css
│   ├── index.tsx
│   ├── logo.svg
│   └── registerServiceWorker.ts
├── images.d.ts
├── package.json
├── README.md
├── tsconfig.json
├── tsconfig.prod.json
├── tsconfig.test.json
└── tslint.json
```

- image.d.ts

  定义了静态资源图片的类型。不定义的话直接import,Typescript会报错`Cannot find module './logo.png'.`

- tsconfig.json

  ts配置

  |   Key   |  Value |
  | :------ | :------|
  |  target | 转换后的目标语言。可选es6 es5。默认es5 |
  |  jsx    | react|
  
### 配置node-sass

> Note: facebook官方推荐使用node-sass-chokidar  
- node-sass has been reported as having the following issues:  
- node-sass --watch has been reported to have performance issues in certain conditions when used in a virtual machine or with docker.  
- Infinite styles compiling [#1939](https://github.com/facebookincubator/create-react-app/issues/1939)

- node-sass has been reported as having issues with detecting new files in a directory [#1891](https://github.com/sass/node-sass/issues/1891)

- node-sass-chokidar is used here as it addresses these issues.

这里有一个问题就是node-sass-chokidar依赖node-sass包。所以执行`npm i node-sass-chokidar -S`时可能会提示安装node-sass错误。无论使用`npm`或`cnpm`或`yarn`都会有这个情况。此处的解决方案如下：

1. 先将registry指向淘宝的镜像源

    ``` bash
    #npm
    npm config set registry http://registry.npm.taobao.org
    #yarn
    yarn config set registry http://registry.npm.taobao.org

    ```

2. 指定node-sass二进制文件下载源：

    ``` bash
    #npm
    npm config set sass-binary-site http://npm.taobao.org/mirrors/node-sass
    #yarn
    yarn config set sass-binary-site http://npm.taobao.org/mirrors/node-sass

    ```

或者可以通过执行`node -p "[process.platform, process.arch, process.versions.modules].join('-')"`。执行结果为`win32-x64-57`。到淘宝镜像[https://npm.taobao.org/mirrors/node-sass/](https://npm.taobao.org/mirrors/node-sass/)下载node-sass的对应名称的二进制文件`win32-x64-57_binding.node`。再设置sass二进制文件路径

```bash
#npm
npm config set sass-binary-path e:/web/win32-x64-57_binding.node
#yarn
yarn config set sass-binary-path e:/web/win32-x64-57_binding.node
```

最后执行`npm i node-sass-chokidar -S`进行安装

>Note: 由于此处node-sass依赖绑定了本地文件，将无法更新node-sass.所以尽量考虑第一种方案

## 将项目跑起来

### 定义处理sass的`npm scripts`

由于我们上面安装了node-sass模块。将css文件全部改为scss后，需要在项目的package.json中增加处理scss的scripts

``` JSON
"scripts": {
+    "build-css": "node-sass-chokidar src/ -o src/",
+    "watch-css": "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive",
     "start": "react-scripts start",
     "build": "react-scripts build",
     "test": "react-scripts test --env=jsdom",
```

``` bash
#将src目录下的scss文件编译后输出css文件到src下的同级目录
node-sass-chokidar  src/ -o src/
```

``` bash
#以递归方式监视src目录下的scss文件。并在同级下生成css文件
node-sass-chokidar src/ -o src/ --watch --recursive
```

>Note: 如果在scss文件中想alias引入某个文件夹下的样式文件。可配置 `node-sass-chokidar --include-path ./src --include-path ./node_modules src/ -o src/`

就可以像这样引入

``` scss
@import 'styles/_colors.scss'; //假设styles目录在src下
@import 'bootstrap/index.scss'; //从node_modules目录下引入bootstrap下的样式文件
```

接着就可以修改App.scss，之后src目录下就会生成css文件。由于js里引入的依旧是css，所以其他都不用改动。

> 经验: 这里使用Vscode可能会出现一个兼容性问题。就是vscode在编辑文件时会将文件处于lock状态，按下保存后由于node-sass-chokidar一直在后台监听文件改动，此时文件处于lock状态，就会就会报文件读写错误

``` bash
=> changed:  
I:\self\my-app\src\App.scss
{
  "status": 3,
  "message": "File to read not found or unreadable: I:/self/my-app/src/App.scss",
  "formatted": "Internal Error: File to read not found or unreadable: I:/self/my-app/src/App.scss\n"
}
```

>解决方案：  
使用node-sass-chokidar的watch轮询配置选项,
`use-polling`表示监听使用轮询方式
`polling-interval`代表文件预热间隔(大致理解为监听间隔) 默认100

``` JSON
'  --use-polling              Watch using polling (chokidars\'s polling option)',
'  --polling-interval         Interval of filesystem folling if polling is being used'
  ```

所以最终的`npm scripts`可能是这样

``` JSON

"scripts": {
    "build-css": "node-sass-chokidar src/ -o src/",
    "watch-css": "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive --use-polling --polling-interval 200",
    "start-js": "react-scripts-ts start",
    "build-js": "react-scripts-ts build",
    "start": "npm-run-all -p watch-css start-js",
    "build": "npm-run-all build-css build-js",
    "test": "react-scripts-ts test --env=jsdom",
    "eject": "react-scripts-ts eject"
  },
```

### 将`npm srcipts`流程打通

我们想`npm start`就能完成sass编译以及运行react-scripts提供的配置。此时我们需要安装`npm-run-all`

``` bash
#npm
npm i npm-run-all -S
#yarn
yarn add npm-run-all
```

然后将`start`和`build`改成包含css预处理的命令

``` json
"scripts": {
     "build-css": "node-sass-chokidar src/ -o src/",
     "watch-css": "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive",
-    "start": "react-scripts start",
-    "build": "react-scripts build",
+    "start-js": "react-scripts start",
+    "start": "npm-run-all -p watch-css start-js",
+    "build-js": "react-scripts build",
+    "build": "npm-run-all build-css build-js",
     "test": "react-scripts test --env=jsdom",
     "eject": "react-scripts eject"
   }
```

执行`npm start`