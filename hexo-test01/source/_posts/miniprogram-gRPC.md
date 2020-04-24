---
title: 关于小程序端使用gRPC的探索
tags: [小程序]
categories: learning
toc: true
mathjax: true
---
对小程序是否能使用gRPC的探索结果
<!-- more -->

### 方案一：使用grpc-mp

grpc官网上提供了grpc-web这一解决方案，用来在web端使用grpc。但是，由于小程序不支持Function 构造器，也没有window这一概念，所以按照grpc-web的方式生成js/ts，无法直接在小程序端使用。

多年面对搜索引擎编程的经验，让我成功找到了[grpc-mp](https://github.com/11os/grpc-mp)这个库，大佬通过对grpc-web的源码进行修改后，让我们也能使用protoc命令直接输出小程序可以使用的grpc的js/ts文件，具体使用方法以及原理，在[github仓库](https://github.com/11os/grpc-mp)中都有，我就不做搬运工了。

#### 可能存在的弊端：

由于grpc-mp是对grpc-web的源码进行修改而来，并且只是单人维护的库（对大佬无意冒犯），以及更新迭代可能跟不上grpc本身迭代的速度，所以使用上可能会存在一定的风险。

### 方案二：使用小程序云函数

因为觉得方案一可能不够稳妥，我又到小程序的开放社区中，试图找到别人现有的gRPC解决方案，看了一圈，大部分都是在问能否实现的，不过在一个帖子中[点此直达](https://developers.weixin.qq.com/community/develop/doc/000e4452b7c608aa63c7282b451400?highLine=grpc)，我发现有官方人员称小程序的云函数可以使用gRPC。

既然官方的人都说了，那就试试吧。云函数是在小程序云开发的一部分，所谓云开发，我感觉就是一套小程序的serverless服务方案吧。因为云函数使用的是Nodejs环境，用的也是js，所以对于前端来说上手不难。但是在开始的时候我还是走了一些弯路，因为一开始我都是照着grpc-web的方式在写，所以各种报错，直到出现了这个错误：

``` 
ReferenceError: XMLHttpRequest is not defined

```

害，整了半天，我忘了grpc-web是要在browser下运行的，可现在这是node环境呀。但是也还好，grpc官网中也提供了node使用grpc的方法，甚至比起grpc-web，使用起来更加方便，[文档地址](https://grpc.io/docs/quickstart/node/)。

#### 踩到的坑：

因为云函数使用的nodejs是在linux环境下的，所以像我这样用windows开发的同学，可能会看到类似这样的错误：

``` 
Error: Failed to load gRPC binary module because it was not installed for the current system

Expected directory: node-v57-linux-x64-glibc

Found: [node-v64-win32-x64-unknow]
```

解决这个问题也容易，修改一下npm install 语句即可：

``` 
npm i --production --unsafe-perm --target=8.9.0 --target_platform=linux --target_arch=x64 --target_libc=glibc --update-binary
```

### 小结：

对于gRPC的使用，这只是刚刚开了个头，还是要好好看文档，多多实践。
