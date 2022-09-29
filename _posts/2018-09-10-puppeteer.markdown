---
layout: post
title:  puppeteer 完成极验验证
date:   2018-09-10
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. 
img:  post-5.jpg # Add image post (optional)
tags: [Blog, puppeteer]
author: wangzm # Add name author (optional)
---
[源码点击这里](https://github.com/wangzm123/resource/blob/master/index.js)
<!-- TOC -->

- [安装puppeteer](#安装puppeteer)
- [项目中使用的基本API](#项目中使用的基本api)
  - [puppeteer.launch](#puppeteerlaunch)
  - [page](#page)
  - [mouse](#mouse)
- [核心代码的实现与原理](#核心代码的实现与原理)

<!-- /TOC -->





# 安装puppeteer
  - 执行cnpm install puppeteer
  - puppeteer 所有的操作基本都是异步的，所以需要使用 async, await  

# 项目中使用的基本API
## puppeteer.launch
  - puppeteer.launch 启动 Chromium 实例
  - puppeteer.launch(options)
    - headless  无头模式
    - slowMo 调整 puppeteer 的操作速度，以便在调试阶段可以看清楚发生了什么
    - devtools 打开控制台

## page
  - browser.newPage() 创建 page 实例
  - page.setViewPort() 设置页面的大小，一个 browser 实例可以有多个 page 实例， page 实例可是设置各自的大小
  - page.setJavaScriptEnavled() 设置页面是否禁用JS
  - page.goto() 页面导航
  - page.type(selector, value[,options]) selector 要输入元素的选择器，value 要输入的值，options.delay 输入没个字符的间隔，默认为0.每个字符的输入都会触发keydown, keypress/input,keyup 事件
  - page.waitFor() 根据入参不同，效果也不同。
    -  入参为 string, 认为是 css 选择器或者 xpath, 根据是否包含‘//’进行判断， 为page.waitForSelector 或者 page.waiteForXPath 的缩写
    - 入参为 function, 是 page.waitForFunction 的缩写
    - 入参为 number，是认为是超时时间，在指定时间后返回 resolve
  - page.$eval(selector, function[,...args])
    - selector 为选择器
    - funciton 为在浏览器实例上下执行的方法
    - ...args 为给 fucntion 传的参数
  - page.evalute(function[,...args])
    - function 在页面实例上下文中执行的方法
    - ...args 传给function 的参数
    - 返回 function 的执行结果

## mouse
  - 通过 page.mouse 创建 mouse 实例， 该实例是在相对于窗口左上角， 基于 css 像素运行的。即具体操作的时候，要制定当前操作的位置
  - mouse.click(X, Y[,options])
    - X, Y 为要点击的位置，
    - options
  - mouse.down([options])
  - mouse.move(X, Y[,options])
    - X,Y 为要移动到的位置
    - options.steps 控制移动的速度
  - mouse.up([options])

# 核心代码的实现与原理
  - 极验图片验证的原理是 canvas 两个图层，完整的背景图片（img1），一个是带缺口的图片(img2)。我们需要拿到 img2 的缺口位置，操作鼠标拖动至这个位置即可。
    - 计算图片位置： 获取 img1, img2 的每个相同像素点的颜色，比较颜色差异，差异较大的视为可能存在的位置
    ```javascript
    await page.evalute(() => {
      const img1 = document.querySelector('.geetest_canvas_fullbg')
      const img2 = document.querySelector('.geetest_canvas_bg')
      const piexDifrence = 30
      let res = []
      for (let i = 50; i<260; i++) {
        for (let j = 0; j<160; j++) {
          const ctx1 = img1.getContext('2d').getImageData(i, j, 1, 1)
          const ctx2 = img2.getContext('2d').getImageData(i, j, 1, 1)
          const data1 = ctx1.data
          const data2 = ctx2.data
          const res1 = Math.abs(data1[0] - data2[0])
          const res2 = Math.abs(data1[1] - data2[1])
          const res3 = Math.abs(data1[2] - data2[2])
          if (res1 > piexDifrence && res2 > piexDifrence && res3 > piexDifrence) {
            if (res.indexOf(i) == -1) {
              res.push(i)
            }
          }
        }
      }
      /** 此最大值最小值需调试*/
      return {min: res[0] - 5, max: res[res.length -1] -50}
    })
    ```
    - 模拟用户操作，拖动图片
    ```javascript
    const target = await page.$('.geetest_slider_button')
    const start = target.boundingBox()
    /** 得到的原始点为按钮的左上角，要调整到按钮的中间，确保点击有效*/
    const startX = start.x + 10
    const startY = start.y + 10
    /** 获取移动距离的数组*/
    const distance = await calculateDistance()
    const targetX = startX + distance.min
    const targetY = startY
    /** 此处操作要尽可能的模拟用户操作，不可直接把图片拉到指定位置，否则会失败*/
    await page.mouse.click(startX, startY, {delay: 2000})
    await page.mouse.down()
    await page.mouse.move(targetX + 30, targetY, {steps: 20})
    await page.waitFor(800)
    await page.mouse.move(targetX, targetY, {steps: 30})
    ```
    - 判断拉动图片的结果，处理返回的错误信息
    ```javascript
    const result = page.evalute(() => {
      const isSuccess = document.querySelector('.geetest_success_animate')
      const isFinish = document.querySelector('.geetest_radar_tip_content').innerHtml.indexOf('尝试过多') > -1
      /**此处可定义多种错误*/
    })
    return result
    ```
  






