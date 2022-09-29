---
layout: post
title:  vscode format 处理过程
date:   2018-06-05
img: green.png
tags: [Blog, vscode]
author: wangzm
---

# vscode 格式化代码，与项目的 eslint 验证保持一致
- vscode 安装 prettier,对代码进行格式化
- 配置setting.json 文件
```javascript
 "editor.defaultFormatter": "esbenp.prettier-vscode"
```
- 用 eslint 对 javascript 代码进行校验
  - vscode  安装 eslint 插件
  - 项目安装 eslint 及相关包, 执行 npx eslint --init, 选择eslint 的规则
- 让 prettier 根据 eslint 的校验结果，对代码进行格式化，修改setting.json
```javascript
"editor.defaultFormatter": "esbenp.prettier-vscode",
"prettier.eslintIntegration": true
```
- 校验并格式化 vue 文件， 需要安装 vetur, 默认配置：
```javascript
 "vetur.format.defaultFormatter.html": "prettyhtml",
  "vetur.format.defaultFormatter.css": "prettier",
  "vetur.format.defaultFormatter.postcss": "prettier",
  "vetur.format.defaultFormatter.scss": "prettier",
  "vetur.format.defaultFormatter.less": "prettier",
  "vetur.format.defaultFormatter.stylus": "stylus-supremacy",
  "vetur.format.defaultFormatter.js": "prettier",
  "vetur.format.defaultFormatter.ts": "prettier"
```


