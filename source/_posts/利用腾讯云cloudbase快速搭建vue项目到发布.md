---
title: 利用腾讯云cloudbase快速搭建vue项目到发布
date: 2022-01-28 15:43
tags:
---
## 前言
利用腾讯云的serverless技术--[cloudbase](https://docs.cloudbase.net/)快速搭建项目到部署发布。
作为一个前端开发，很多时候因为没有后端服务的接口和数据库支持而一筹莫展；一想到搭建一个项目，还要购买服务器、配置数据库等等就觉得麻烦；
现在，腾讯云提供了一种新的快速上线的云架构，让只会前端的同学也能快速开发一个完整的前后端系统，并且能够支持一键部署，快速上线，低成本、高效率的开发。
## 搭建环境
### 一、开通环境
进入[云开发 CloudBase 控制台](https://console.cloud.tencent.com/tcb)开通CloudBase，腾讯云直接微信扫码登录注册就行。

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643350234186.png)

选择vue应用模板；

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643350311797.png)

创建环境名称test，计费的话前期有免费额度，然后点击下一步到开通为止。

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643350465793.png)

点击应用进入环境总览；

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643350571129.png)

后面的话主要会用到的就是当前环境的id；

### 二、搭建vue前端项目及一键部署
#### 1、全局安装 CloudBase CLI
```bash
npm install -g @cloudbase/cli@latest
```

#### 2、创建vue项目
```bash
npx vue create vue-hello-world
```

#### 3、发布项目
```bash
cd vue-hello-world
cloudbase framework deploy -e test-4g5kb8yja12f81fb

//或者也可以直接输入cloudbase命令回车，test-4g5kb8yja12f81fb 为刚刚创建的环境id

 Tip: cloudbase 命令可以简写为 tcb 

CloudBase CLI 1.12.0
CloudBase Framework 1.9.0
   ______ __                   __ ____                             
  / ____// /____   __  __ ____/ // __ ) ____ _ _____ ___           
 / /    / // __ \ / / / // __  // __  |/ __ `// ___// _ \          
/ /___ / // /_/ // /_/ // /_/ // /_/ // /_/ /(__  )/  __/          
\____//_/_\____/ \__,_/ \__,_//_____/ \__,_//____/ \___/       __  
   / ____/_____ ____ _ ____ ___   ___  _      __ ____   _____ / /__
  / /_   / ___// __ `// __ `__ \ / _ \| | /| / // __ \ / ___// //_/
 / __/  / /   / /_/ // / / / / //  __/| |/ |/ // /_/ // /   / ,<   
/_/    /_/    \__,_//_/ /_/ /_/ \___/ |__/|__/ \____//_/   /_/|_|  
                                                                   

 CloudBase Framework  info     Version v1.9.0
 CloudBase Framework  info     Github: https://github.com/Tencent/cloudbase-framework

 CloudBase Framework  info     EnvId test1-4g5kb8yja12f81fb
 CloudBase Framework  info     Region ap-shanghai
? 请输入应用唯一标识(支持 A-Z a-z 0-9 及 -, 同一环境下不能相同) test
? 检测到当前项目包含 Vue.js 项目

  🔨 构建脚本 `npm run build`
  📦 本地静态文件目录 `dist`

  是否需要修改默认配置 No
? 是否需要保存当前项目配置到项目中 Yes
 CloudBase Framework  info     Validate config file success.
 CloudBase Framework  info     AppName test
 CloudBase Framework  info     📦 install plugins
 CloudBase Framework  info     callHooks 'preDeploy' 
 CloudBase Framework  info     🔧 init: vue...
 CloudBase Framework  info     Website 插件会自动开启静态网页托管能力，需要当前环境为 [按量计费] 模式
 CloudBase Framework  info     Website 插件会部署应用资源到当前静态托管的 / 目录下
 CloudBase Framework  info     🔨 build: vue...
 CloudBase Framework  info     running 'npm install --prefer-offline --no-audit --progress=false' 

up to date in 594ms

11 packages are looking for funding
  run `npm fund` for details
 CloudBase Framework  info     running 'npm run build' 

> vue-hello-world1@0.1.0 build
> vue-cli-service build




 DONE  Compiled successfully in 1669ms                                                                                      下午2:34:11

  File                                 Size                                           Gzipped

  dist/js/chunk-vendors.4102dc7c.js    94.96 KiB                                      34.02 KiB
  dist/js/app.ecf66627.js              4.58 KiB                                       1.63 KiB
  dist/css/app.fb0c6e1c.css            0.33 KiB                                       0.23 KiB

  Images and other types of assets omitted.

 DONE  Build complete. The dist directory is ready to be deployed.
 INFO  Check out deployment instructions at https://cli.vuejs.org/guide/deployment.html
      
 CloudBase Framework  info     🧬 compile: vue...
 CloudBase Framework  info     callHooks 'postCompile' 
正在部署[░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░] 100% 100.2 s
 CloudBase Framework  info     🚀 deploy: vue...
 CloudBase Framework  info     🚀 网站部署成功
 CloudBase Framework  info     callHooks 'postDeploy' 
 CloudBase Framework  info     🌐 应用入口信息:
🔗 网站入口: https://test-4g5kb8yja12f81fb-1256759236.tcloudbaseapp.com/
 CloudBase Framework  info     ✨ done
```

部署结束后会生成一个访问地址：🔗 网站入口: https://test-4g5kb8yja12f81fb-1256759236.tcloudbaseapp.com/

复制到浏览器地址栏就直接能访问啦~

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643352051889.png)

### 三、配置数据库
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643352786244.png)

新建一个名为test的集合数据库；

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643352881859.png)

添加文档(创建表)、添加字段

### 四、开发项目(前端操作数据库)
进入项目根目录，引入cloudbase依赖

```base
#npm
npm install @cloudbase/js-sdk -S
# yarn
yarn add @cloudbase/js-sdk
```

编写查询和新增方法

```js
<template>
  <button @click="insert">插入一条数据</button>
  <button @click="search">查询数据</button>
  <span v-if="result">{{result}}</span>
</template>

import cloudbase from '@cloudbase/js-sdk';
const app = cloudbase.init({
  env: "test-4g5kb8yja12f81fb"
});
const db = app.database();

methods: {
     search() {
      let _this = this
      db.collection("test") // collection() 方法获取一个集合的引用
      .get() // get() 方法会触发网络请求，往数据库取数据
      .then(function (res) {
        _this.result = res
      });
    },		
    insert(){
      db.collection("test").add({
        name: 'test',
        password: '123456'
      }).then(res=>{
        alert( JSON.stringify(res),"插入成功")
      })
    }
  }
```

本地测试接口调用，发现没有权限 Permission denied

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643353831460.png)

#### 配置权限

进入控制台登录授权
官方提供了几种[登录授权](https://docs.cloudbase.net/authentication/auth/introduce)
当前我们使用未登录模式

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643354158012.png)

在根目录下public/index.html 初始化cloudbase

```html
<script src="//imgcache.qq.com/qcloud/cloudbase-js-sdk/1.6.0/cloudbase.full.js"></script>
<script>
  const app = cloudbase.init({
    env: 'test-4g5kb8yja12f81fb' // 您的环境id
  })
</script>
```

但是这样做还是不能访问到数据库，因为还需要配置数据库的读写权限。
进入数据库权限设置。

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643354016007.png)

点击右上角切换到安全规则权限设置，按照如下配置

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643354464046.png)

```json
{
  "read": "doc._openid == auth.uid || auth == null",
  "write": "doc._openid == auth.uid || auth == null"
}
```

此时点击查询数据和查询，就能够顺利调用接口查询到数据了。

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643354621900.png)

### 五、发布项目及配置域名
在根目录下再次输入命令 cloudbase framework deploy

```bash
cloudbase framework deploy
```
cloudbase 又会执行npm run build 打包并发布部署到线上环境。


#### 自定义域名
进入静态网站托管，进入详细配置。

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643355011560.png)

点击添加域名

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643355403629.png)

域名必须为https协议，所以提前准备好带有证书的域名。

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643355559005.png)

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643355656598.png)

还需要配置DNS CNAME记录。

等待部署成功就可以访问啦~
https://test.vanterc.com/

![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/image_1643356325651.png)