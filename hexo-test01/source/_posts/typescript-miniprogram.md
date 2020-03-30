---
title: 使用 typescript 与 vant-weapp 制作小程序的一次尝试
tags: [小程序]
categories: learning
toc: true
mathjax: true
---
借助 typescript 3.8.3 与 vant-weapp UI框架 制作一款带有三天天气预报的日历小程序
<!-- more -->

### 使用 typescript 3.8.3 创建小程序

目前微信小程序官方文档中已经有了对typescript支持的相关支持，按照文档便可使用typescript愉快地开始编写小程序[详情看这里](https://developers.weixin.qq.com/miniprogram/dev/devtools/edit.html#TypeScript-%E6%94%AF%E6%8C%81)，不过按照文档新建出的项目默认使用的typescript版本是 3.3.3333，这里我们将其改为最新的3.8.3，重新执行 npm install || yarn

#### 注意：

直接使用官方的配置文件时，可能无法使用promise语法，需将tsconfig.json文件中所有的‘es5’改为‘es6’

### 安装 vant-weapp UI框架

Vant Weapp 是移动端 Vue 组件库 Vant 的小程序版本，两者基于相同的视觉规范，提供一致的 API 接口，助力开发者快速搭建小程序应用。

#### 安装步骤：

* 通过 npm 安装

``` 
 npm i @vant/weapp -S --production

```

需要注意的是 package.json 和 node_modules 必须在 miniprogram 目录下

* 构建 npm 包

打开微信开发者工具，点击 工具 -> 构建 npm，并勾选 使用 npm 模块 选项，构建完成后，即可引入组件

* 修改 tsconfig.json

在 tsconfig.json 中增加如下配置，防止 npm 构建后 tsc 编译报错

``` 
{
  "baseUrl": ".",
  "paths": {
    "@vant/weapp/*": ["./node_modules/@vant/weapp/dist/*"]
  }
  ...
}

```

* 修改 app.json

将 app.json 中的 "style": "v2" 去除，小程序的新版基础组件强行加上了许多样式，难以去除，不关闭将造成部分组件样式混乱。

#### 使用方法:

* 以 Button 组件为例，只需要在app.json或index.json中配置 Button 对应的路径即可。

``` 
"usingComponents": {
  "van-button": "@vant/weapp/button"
}
```

更多使用方法，请自行查看[官方文档](https://youzan.github.io/vant-weapp/#/intro)

### 调用天气API

获取天气预报的API网上有很多，这里我使用的是[和风天气](https://dev.heweather.com/)的API，文档说可以获取3-10天的天气预报，但我在开发中发现无论如何修改参数，始终都只返回了三天的天气预报，大概是因为我用的是免费的吧，所以后续可能会考虑使用别的API来做替换

### 发现的问题

* 调用API时间较长

一开始我以为是我的网络问题，但在换了网络之后，仍发现API返回天气预报需要一定时间（可能是返回的数据太多了？）

* UI组件性能问题

因为需要将天气情况显示在日历中，所以使用了<van-calendar>组件的 formatter 方法，但在实际使用中我发现该方案在渲染的过程中存在重复调用的情况，相同的方法重复调用了6次，我觉得可能对小程序的性能存在一定影响··

