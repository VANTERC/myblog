---
title: 写一个最简单的mysql查询接口
date: 2021-02-25 17:17
tags:
---

# egg 写一个最简单的mysql查询接口

按照[egg官网](https://eggjs.org/zh-cn/intro/quickstart.html) 写一个最简单的mysql查询接口(提前准备好mysql数据库)
<!-- more -->
1、创建项目 安装依赖
```bash
mkdir my-study-backend
cd my-study-backend
npm init
npm i egg --save
npm i egg-bin --save-dev
npm i --save egg-mysql
```
修改启动项
```json
"scripts": {
    "dev": "egg-bin dev"
  },
```
2、在app目录下新建app/controller/users.js
```javascript
const Controller = require('egg').Controller;

class UsersController extends Controller {
  async getUsers() {
    const { ctx } = this;
    ctx.body = 'hello world'
  }
}

module.exports = UsersController;
```
3、在app目录下新建app/router.js
```javascript
module.exports = app => {
  const { router, controller } = app;
  router.get('/api/getUsers', controller.users.getUsers); // get('接口路径',controller内对应的方法)
};
```
4、在根目录下新建config/config.default.js
```javascript

module.exports = {
  keys: '123456790', // 随便写个Cookie 安全字符串
};
```
终端运行项目
```bash
npm run dev
```
此时打开浏览器访问接口就会显示hello world
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/1595334065360-6ee5820a-036b-4c33-b1f2-e51b30eb9acf.png)
5、在config目录下新建config/plugin.js 添加mysql配置
```javascript
module.exports = {
  mysql: {
    enable: true,
    package: 'egg-mysql',
  },
};
```
6、修改config/config.default.js 添加数据库账号密码
```javascript
module.exports = {
  keys: '123456790',
  mysql: {
    // 单数据库信息配置
    client: {
      // host
      host: '39.120.202.168',
      // 端口号
      port: '3306',
      // 用户名
      user: 'root',
      // 密码
      password: '123456',
      // 数据库名
      database: 'mystudy',
    },
    // 是否加载到 app 上，默认开启
    app: true,
    // 是否加载到 agent 上，默认关闭
    agent: false,
  }
};
```
并在数据库mystudy内添加一些数据
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/1595334905486-4cdcf583-8243-4eb3-bc2c-430631ea291a.png)
7、在app目录下新建app/service/users.js
```javascript
const Service = require('egg').Service;

class UsersService extends Service {
  async getData() {
    const data = await this.app.mysql.query('SELECT * FROM user');
    return { data };
  }
}

module.exports = UsersService;
```
8、修改app/controller/users.js
```javascript
const Controller = require('egg').Controller;

class UsersController extends Controller {
  async getUsers() {
    const { ctx } = this;
    const res = await ctx.service.users.getData();
    ctx.body = {
      code: '200',
      data: res.data,
      message: 'success'
    };
  }
}

module.exports = UsersController;
```
此时重新运行项目
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/1595335194788-4c1e4d12-41b2-463b-bb36-03ecb8fa8230.png)
成功获取到数据啦！




