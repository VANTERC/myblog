---
title: vue2老项目升级Vite优化提升16倍
date: 2022-11-17 17:57
tags:
---

## 老的vue2项目如何升级到Vite?
很多vue2老项目可能是vue-cli脚手架，甚至有的还是webpack构建的。现在都2022年了，对于刚入行前端的同学来说来花时间去学一遍webpack，费时又费力，苦涩又难懂，一堆的配置项，看的头皮发麻。
而对于上一代vue-cli脚手架，相比webpack易上手很多，vue-ui的可视化界面搭建配置项目也非常友好。
但是，尤大极力推荐Vite才是未来，可以说Vite打遍天下无敌手，是现在最快最好的构建工具。
<!-- more -->
废话不多说了，上图！

老vue2 vue-cli脚手架项目资源下载情况：
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/h/WeChat3915120712479346fd5eed8f2e4c69f6.png)

升级vite后项目资源下载情况：
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/h/WeChatb437da539274f4656abb05d5026522d5.png)

vite构建后面资源体积小了3倍，这里主要也有配置了gzip的功劳。

再来看构建速度对比：
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/h/WeChat04b370310ff4e0130afe2ffe663294b8.png)

Id753构建为老项目构建速度ID754为升级为Vite后的构建速度，构建速度减少了一半！

在来看项目启动速度
老vue2 vue-cli脚手架项目启动速度：16980ms
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/h/WechatIMG6821.png)

升级vite后项目启动速度：621ms
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/h/WechatIMG6822.png)

快了将近16秒，足足提升了16倍时间！大大节省了我们的开发时间。

那么vue2老项目如何升级Vite呢？
大家可能都知道，Vite对vue3的支持比较好，我们利用Vite去创建项目的时候默认都是vue@3+以上的版本，就像这样：
``` json 
"dependencies": {
  "vue": "^3.2.41"
},
"devDependencies": {
  "@vitejs/plugin-vue": "^3.2.0",
  "vite": "^3.2.3"
}
```

那么如何让我们vue2的项目也能用到Vite呢？那么需要一个Vite插件 [vite-plugin-vue2](https://github.com/underfin/vite-plugin-vue2)

如果是老的webpack项目就删除掉以下几个文件，当然删之前先得看下环境配置，以及是否有一些特殊的配置项，这个根据自己的项目实际情况来。
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/h/WX20221114-165721%402x.png)

如果是vue-cli脚手架项目就把vue.config.js改为vite.config.js，webpack项目新增这个配置文件。

然后根据自己项目的环境配置新建环境配置文件：
.evn.development
.env.qa
.env.pre
.env.production

环境文件内主要配置我们各个环境的api域名地址，如：
NODE_ENV='development'
VITE_APP_URL='http://dev.domainapi.com/'
这里必须以VITE_开头，熟悉vue-cli脚手架的可能知道是VUE_开头的
如：VUE_APP_URL='http://dev.domainapi.com/'

然后再在项目中调整配置api，可以用import.meta.env.VITE_APP_API_URL来获取配置文件。

然后调整package.json
删除webpack相关依赖。
删除@vue/cli-相关依赖。

删除package-lock.json

安装vite和vite-plugin-vue2
``` bash
npm install vite@latest
npm install vite-plugin-vue2
```

注：在vite项目里是不支持require引用依赖库的，必须全部调整为import引用。

把我们的项目结构调整为类似如下结构：
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/h/WX20221114-165809%402x.png)

package.json部分如下:
``` json 
...
"scripts": {
  "dev": "vite",
  "build:dev": "vite build --mode development",
  "build:qa": "vite build --mode qa",
  "build:pre": "vite build --mode pre",
  "build:prod": "vite build --mode production",
  "build": "npm run build:prod",
  "preview": "vite preview"
},
"dependencies": {
  ...
  "vue": "^2.6.11",
  "axios": "^0.15.3",
  "iview": "2.13.1",
  "vue-router": "^2.8.1",
  "vuex": "^2.5.0",
},
"devDependencies": {
  ...
  "vite-plugin-vue2": "^2.0.2",
  "vite": "^3.2.3"
}
```

index.html如下：
``` html
<body>
    <div id="app"></div>
    <script type="module" src="/src/main.js"></script>
    <!-- built files will be auto injected -->
  </body>
```

vite.config.js如下：
```js
import { createVuePlugin } from "vite-plugin-vue2";
import viteCompression from 'vite-plugin-compression'; // gizp压缩
import { defineConfig } from "vite";
import path from "path";

/**
 * @param newFilename {string}
 * @returns {import('vite').Plugin}
 * @des 可以重命名打包后index.html文件名
 */
 const renameIndexPlugin = newFilename => {
  if (!newFilename) return

  return {
    name: 'renameIndex',
    enforce: 'post',
    generateBundle(options, bundle) {
      const indexHtml = bundle['index.html']
      indexHtml.fileName = newFilename
    },
  }
}

export default defineConfig({
    base: '/dist/',
    plugins: [
      createVuePlugin(),
      viteCompression(),
      renameIndexPlugin('index_prod.html')
    ],
    envDir:'./env',
    server: {
      host: 'localhost',
      port: 8080,
      open: true
    },
    build:{
      outDir: 'dist',
      assetsDir: 'static',
    },
    resolve: {
      alias: [
        {
          find: '@',
          replacement: path.resolve(__dirname, "src"),
        }
      ]
    }
})
```

这是根据我项目实际配置的，
打包后域名路径必须为dist，正常来说默认不需要配置base项，
打包后的index.html会改为index_prod.html，正常来说可以去掉renameIndexPlugin()
vite-plugin-compression插件为启用gzip压缩，更好的减少我们构建后项目资源的体积。
其他具体的配置项也可以去参考Vite官网。

最后重新删除package-lock.json和node_modules，执行npm install

运行npm run dev 
查看报错情况，根据提示一步一步修改问题。

















