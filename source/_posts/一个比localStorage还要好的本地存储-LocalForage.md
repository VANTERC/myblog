---
title: 一个比localStorage还要好的本地存储 LocalForage
date: 2021-02-25 17:59
tags:
---

localForage 是一个 JavaScript 库，通过简单类似 localStorage API 的异步存储来改进你的 Web 应用程序的离线体验。它能存储多种类型的数据，而不仅仅是字符串。
localForage 有一个优雅降级策略，若浏览器不支持 IndexedDB 或 WebSQL，则使用 localStorage。在所有主流浏览器中都可用：Chrome，Firefox，IE 和 Safari（包括 Safari Mobile）。
<!-- more -->

项目地址:[https://github.com/localForage/localForage](https://github.com/localForage/localForage)
```bash
npm install localforage
```


```javascript
import localforage from "localforage";
window.localforage = localforage;
```


我们先来对比下两者的区别

| 属性 | localStorage | localForage |
| :--- | :--- | :--- |
| 容量 | 5M | 无限制 |
| 类型 | String/Object | 任何类型 |
| 读取方式 | 同步 | 异步 |

1、localForage的容量几乎无限制，而localStorage只能存储大约5M的内容，如果内容存储的过多的话，会导致内存的消耗以及页面变卡，非常影响性能。
2、localForage增删改查的方法几乎和localStorage相同，并且支持回调方法，因为在某些加载存储图片的情况下可能是异步的，所以localForage的异步有效的解决了同步阻塞问题。


```javascript
localforage.getItem('keyname', function (err, value) {
    ...
});
localforage.setItem('keyname', value, function (err, value) {
    ...
});
```


```javascript
localforage.setItem('key', 'value').then(function () {
  return localforage.getItem('key');
}).then(function (value) {
  // we got our value
}).catch(function (err) {
  // we got an error
});
```


3、localStorage一般存储为字符串类型，如果是对象的话，我们需要通过JSON.stringify和JSON.parse转换。但是localForage几乎可以支持任何类型，包括图片base64、blob类型。
4、那么为什么localForage可以存储那么多内容呢？那是因为他内置了驱动，默认情况他会先去利用[IndexedDB](http://www.ruanyifeng.com/blog/2018/07/indexeddb.html)进行存储，如果浏览器不支持就会用[WebSQL](https://www.runoob.com/html/html5-web-sql.html)来存储，最后才用localStorage。当然你也可以利用setDriver()方法设置特定的驱动来存储。


```javascript
localforage.INDEXEDDB
localforage.WEBSQL
localforage.LOCALSTORAGE
```


5、相对于IndexedDB、WebSQL复杂的操作，localforage利用了我们熟悉localStorage增啥改查的方法将数据存储在了IndexedDB、WebSQL、localStorage中，大大减少了开发人员学习的成本，同时也解决了我们本地数据存储时遇到的一些问题。

