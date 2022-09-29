---
layout: post
title: vue-cli 3代码压缩去掉 console.log 信息
date:   2018-09-28
description: You will find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: post-1.jpg # Add image post (optional)
tags: [Blog, vue-cli]
author: wangzm  # Add name author (optional)
---
默认的 vue-cli 3 配置项，打包的 JS 代码，并不会去掉 console 信息，经过查文档，终于搞定。
增加 vue.config.js 的配置项， 使用 uglifyjs-webpack-plugin 压缩代码.此处调试的时候要记得清除浏览器缓存，否则可能已经配置正确了，因为浏览器的缓存机制导致看到的效果不正确。
```javascript
npm install uglifyjs-webpack-plugin --save-dev
// vue.config.js 
cosnt UglifyJsPlugin = require('uglify-webpack-plugin')
// 增加 configureWebpack， 该对象会被 webpack-merge 合并入最终的 webpack 配置
module.exports = {
  configureWebpack: {
    optimization: {
      minimizer: [
        new UglifyJsPlugin({
          uglifyOptions: {
            compress: {
              warnings: false,
              drop_console: true,//console
              pure_funcs: ['console.log']
            }
          }
      })
    ]
    }
  }
}

```
* uglifyOptions 配置项看[这里]('https://github.com/mishoo/UglifyJS2#minify-options')
* compress 配置项看[这里]('https://github.com/mishoo/UglifyJS2#compress-options')
