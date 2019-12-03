# 热更新原理

## 概念
根据[官网](https://www.webpackjs.com/guides/hot-module-replacement/)的理解，HMR是指程序在运行过程中替换、删除、添加模块，而无需重新加载整个页面，从而提高开发速度，优化开发体验（只适用于开发环境）。webpack-dev-server还提供了一些[API](https://www.webpackjs.com/api/hot-module-replacement/)，用来进行一些自定义配置，比如哪些模块的变化强制刷新页面，哪些模块变化不刷新页面而是走热更新，实际开发中很少需要自定义配置，因此我们这里重点介绍原理。

我们可以先设想一下，webpack是怎么实现热更新的，实现热更新需要什么条件。首先肯定要知道模块发生了改变，然后拿到改变了的模块chunk，再去替换调老的模块chunk，由于模块在编译后已经面目全非，很难辨认，在这个过程中还要知道模块的映射位置信息。

## 使用
webpack.config.js
```js
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    mode: 'development',
    entry: {
        'app': path.resolve(__dirname, './index.js'),
    },
    devServer: {
        hot: true,
    },
    plugins: [
        new HtmlWebpackPlugin(),
        new webpack.HotModuleReplacementPlugin()
    ]
}
```
package.json
```json
{
    //...
    "scripts": {
        "start": "webpack-dev-server"
    },
    "dependencies": {
        "webpack": "^4.41.2"
    },
    "devDependencies": {
        "html-webpack-plugin": "^3.2.0",
        "webpack-dev-server": "^3.9.0"
    }
    //...
}
```
npm run start启动服务，改变index.js文件，ctrl+s，就能看到热更新效果，在控制台也能看到热更新相关的请求。
![avatar](./imgs/02/1.jpg)
