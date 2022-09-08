---
title: vue项目启用压缩gzip
date: 2021-04-06 11:19
tags:
---
## 1、项目中配置
Vue-cli3.0项目
安装依赖：compression-webpack-plugin
<!-- more -->
```bash
npm install compression-webpack-plugin --save-dev
```

```javascript
const CompressionPlugin = require('compression-webpack-plugin');  
const productionGzipExtensions = /\.(js|css|json|txt|html|ico|svg)(\?.*)?$/i;  
module.exports = {
    publicPath: './',
    productionSourceMap: false,
    configureWebpack: {...},
    chainWebpack: config => {
        config.resolve.alias.set('@', resolve('src'));
        if (process.env.NODE_ENV === 'production') {
            config.plugin('compressionPlugin')
            .use(new CompressionPlugin({
                filename: '[path].gz[query]',
                algorithm: 'gzip',
                test: productionGzipExtensions,
                threshold: 10240,
                minRatio: 0.8,
                deleteOriginalAssets: true
            }));
        }
    },
};

```
## 2、nginx配置
服务器启用gzip
/etc/nginx/nginx.config
```bash
gzip on;  
gzip_static on;  
gzip_min_length 1k;  
gzip_buffers 4 32k;  
gzip_http_version 1.1;  
gzip_comp_level 2;  
gzip_types text/plain application/x-javascript text/css application/xml;  
gzip_vary on;  
gzip_disable "MSIE [1-6].";

```
然后重启服务器 nginx -s reload
gzip使用环境:http,server,location,if(x),一般把它定义在nginx.conf的http{…..}之间
　　gzip on
　　on为启用，off为关闭
　　gzip_min_length 1k
　　设置允许压缩的页面最小字节数，页面字节数从header头中的Content-Length中进行获取。默认值是0，不管页面多大都压缩。建议设置成大于1k的字节数，小于1k可能会越压越大。
　　gzip_buffers 4 16k
　　获取多少内存用于缓存压缩结果，‘4 16k’表示以16k*4为单位获得
　　gzip_comp_level 5
　　gzip压缩比（1~9），越小压缩效果越差，但是越大处理越慢，所以一般取中间值;
　　gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php
　　对特定的MIME类型生效,其中'text/html’被系统强制启用
　　gzip_http_version 1.1
　　识别http协议的版本,早起浏览器可能不支持gzip自解压,用户会看到乱码
　　gzip_vary on
　　启用应答头"Vary: Accept-Encoding"
　　gzip_proxied off
　　nginx做为反向代理时启用,off(关闭所有代理结果的数据的压缩),expired(启用压缩,如果header头中包括"Expires"头信息),no-cache(启用压缩,header头中包含"Cache-Control:no-cache"),no-store(启用压缩,header头中包含"Cache-Control:no-store"),private(启用压缩,header头中包含"Cache-Control:private"),no_last_modefied(启用压缩,header头中不包含"Last-Modified"),no_etag(启用压缩,如果header头中不包含"Etag"头信息),auth(启用压缩,如果header头中包含"Authorization"头信息)
　　gzip_disable msie6
　　IE5.5和IE6 SP1使用msie6参数来禁止gzip压缩 )指定哪些不需要gzip压缩的浏览器(将和User-Agents进行匹配),依赖于PCRE库
注：（gzip_static on）Nginx的动态压缩是对每个请求先压缩再输出，这样造成虚拟机浪费了很多cpu，解决这个问题可以利用nginx模块Gzip Precompression，这个模块的作用是对于需要压缩的文件，直接读取已经压缩好的文件(文件名为加.gz)，而不是动态压缩，对于不支持gzip的请求则读取原文件。
　　1.文件可以使用 gzip 命令来进行压缩，或任何其他兼容的命令。
　　2.gzip_static配置优先级高于gzip。
　　3.开启nginx_static后，对于任何文件都会先查找是否有对应的gz文件。
　　4.gzip_types设置对gzip_static无效。
　　5.gzip static默认适用HTTP 1.1。
参考：https://www.cnblogs.com/zigood/p/12504401.html