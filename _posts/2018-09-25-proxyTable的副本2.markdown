---
layout: post
title:  webpack proxyTable 代理转发配置
date:   2018-09-25
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. 
img: Wechat.png # Add image post (optional)
tags: [wx]
author: wangzm # Add name author (optional)
---
------------
```javascript
proxyTable: {
  '/api': {
    target: 'www.app1.com:8001',
    changeOrigin: true,
    pathRewrite: {
      '^/api': '/index/index'
    }
  }
}
```
* 当请求‘/api/login’,实际上请求的是‘www.app1.com:8001/index/index/login’

