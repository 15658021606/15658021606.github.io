---
layout: post
title:  微信小程序入坑指南
date:   2018-06-07
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. 
img: Wechat.png # Add image post (optional)
tags: [wx]
author: wangzm # Add name author (optional)
---
### 一、微信项目起步
  1、注册小程序，获取小程序appid。  
  2、按照发开文档嗖嗖的就能建好一个基础模板  
  3、登录[微信小程序公众平台](https://mp.weixin.qq.com),可在 设置中查看小程序AppId, 在用户身份中设置不同账号的权限。  

### 二、小程序项目结构
  .js 后缀文件是脚本文件  
  .json 后缀文件是配置文件  
  .wxml 后缀文件是模板文件，类似HTML文件
  .wxss 后缀文件是样式表文件，类似CSS文件
###### 全局
  app.js  小程序的脚本代码， 可以在这里监听处理小程序的生命周期，声明全局变量。   
  app.json 小程序的全局配置文件，可配置窗口颜色，标题，导航。  
  app.wxss 小程序的公共样式表。  
###### 页面
  page.js  小程序当前页面的脚本代码。Page 是一个页面构造器，小程序框架会把 data 和 .wxml 文件渲染出来。页面加载完成，会有 onload 回调，页面初次渲染完成会有 onReady 以及其它生命周期函数和事件处理函数。  
  page.json 小程序当前页面的配置文件，window 项里面的内容。    
  page.wxss 小程序的当前页面公共样式表。  
  
### 三、小程序开发需注意的问题  

  1、设置 tabBar 但是不显示。  
    解决： 在 pages 的第一项，一定要在 tabBar -> list 里面，要不然是不会显示的。

  2、pc 预览图片正常显示，但是真机不显示。  
    解决： image 组件的 src 图片不能有空格。

  3、事件绑定传参的时候， data-XX 不能试用驼峰命名， 在e 参数里面统一都是小写。 

  4、当wx.showThoast() 遇上 wx.hideLoading(), 会直接结束掉。所以，不要偷懒在 requst     的 complete 里面执行hideLoading();  

  5、产品希望所有页面的返回按钮，都指向首页。   
    A => b  => c => d , b, c, d 的返回都指向 A   
    处理办法：  A => b, wx.navigateTo();
              b => c => d, wx.redirectTo(); 把中间的页面全部销毁掉，这样返回的就是首页了

  6、flex 布局， 当宽度不够时，元素会被等比缩小。可能会出现元素内容放不下被挤掉的情况。此时可通过对内容不够放的元素设置 flex-shrink: 0, 对其他的元素设置 flex-shrink: 1， 使得为   1  的元素进行缩放， 为0 的元素保持不变。 

  7、IPX 的适配问题。IPX 的横条，会导致误操作触发小程序设置的底部按钮。  
  可通过wx.getSystemInfo 来判断当前机型是否为IPX，在IPX 的底部增加68rpx的安全高度。  

 8、微信小程序定位的问题。微信获取用户经度纬度信息，请求高德API进行逆地址解析，得到位置省市信息。再拿获取到的省市信息与后盾啊数据库的内容查找匹配的cityId。  

```javascript 
getLocation: () => {
    console.log(12345);
    return new Promise((resolve, reject) => {
      wx.getLocation({
        success: res => {
          console.log(res);
          const { longitude, latitude} = res;
          wx.request({
            url: `https://restapi.amap.com/v3/geocode/regeo?key=29ee6f42defaeec31b12762bd08b1d7b&location=${longitude},${latitude}`,
            success: locationRes => {
              console.log(locationRes)
              resolve(locationRes.data.regeocode.addressComponent.city)
            },
            fail: locationRes => {
              reject('地址解析失败')
            }
          })
        },
        reject : res => {
          console.log('fail', res)
        }
      })
    })
  },
  getCityList: () => {
    return new Promise((resolve, reject) => {
      wx.request({
        url: `api/gityList`,
        success:(res) => {
          resolve(res);
        } ,
        fail: res => {
          reject('获取城市列表失败')
        }
      })
    })
  }
  Promise.all([this.getCityList(), this.getLocation()])
    .then(res => {
      console.log(res)
    })
    .catch(e => {
      console.log(e)
    })
``` 

9、Promise 增加 done 方法,处于回调链的尾端，保证抛出任何错误的可能。  
```javascript
Promise.prototype.done = function (onfulfilled, onReject) {
  this.then(onFulfilled, onReject) 
    .catch(fucntion (reason) {
      setTimeout(() => {
        throw reason
      }, 0);
    })
}
```
10、使用箭头函数解决 this 的问题




  


