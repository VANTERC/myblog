---
title: egg 写一个最简单的token登陆验证
date: 2021-02-25 18:32
tags:
---

# egg 写一个最简单的token登陆验证

首先看完整的目录结构
<!-- more -->
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/egg1.png)
主要实现的功能是通过账号密码登录获取token，再通过token来获取用户信息，一共两个接口：登陆接口和获取用户信息接口。
上一篇文章已经写到了如何写一个最简单的获取mysql用户信息的接口，现在直接在上一篇文章的基础上做token验证登陆。


mysql数据库准备好数据
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/egg2.png)




1、安装jwt依赖和解决跨域、白名单egg-cors
```bash
npm i egg-jwt --save
npm i egg-cors --save
```
2、config/config.default.js
```javascript

module.exports = {
  // 配置需要的中间件，数组顺序即为中间件的加载顺序
  // middleware: [ 'jwt' ],

  keys: '123456790',
  mysql: {
    // 单数据库信息配置
    client: {
      // host
      host: '38.130.21.169',
      // 端口号
      port: '3306',
      // 用户名
      user: 'root',
      // 密码
      password: 'yourpassword',
      // 数据库名
      database: 'database',
    },
    // 是否加载到 app 上，默认开启
    app: true,
    // 是否加载到 agent 上，默认关闭
    agent: false,
  },
  jwt: {
    secret: "123456"
  },
  security: {
    csrf: {
      enable: false,
      ignoreJSON: true
    },
    domainWhiteList: ['http://localhost:7001'],//允许访问接口的白名单
  },
  cors: {
    origin:'*', // 解决跨域问题
    allowMethods: 'GET,HEAD,PUT,POST,DELETE,PATCH'
  },
  cluster: {
    listen: {
      port: 7001, // 监听端口
      // hostname: '127.0.0.1', // 不建议设置 hostname 为 '0.0.0.0'，它将允许来自外部网络和来源的连接，请在知晓风险的情况下使用
      // path: '/var/run/egg.sock',
    }
  }
};
```
3、config/plugin.js
```javascript
module.exports = {
  mysql: {
    enable: true,
    package: 'egg-mysql',
  },
  jwt: {
    enable: true,
    package: 'egg-jwt',
  },
  cors: {
    enable: true,
    package: 'egg-cors',
  },
};
```
4、app/router.js 定义登陆路由
```javascript
module.exports = app => {
  const { router, controller, middleware} = app;
  const jwtErr = middleware.jwtErr(app.config.jwt)
  
  router.get('/api/getUser', jwtErr, controller.user.getUser);
  router.post('/api/login', controller.login.login);
};
```
在获取用户信息接口上使用中间件


5、middleware/jwtErr.js 定义一个jwt中间件 访问路由的时候会通过这个中间件来处理
```javascript
module.exports = (options, app) => {
  return async function jwtErr(ctx, next) {
    const token = ctx.request.header.token;
    if(token){
      try {
        ctx.app.jwt.verify(token, options.secret); // 验证token 
        await next();
      } catch (error) {
        ctx.status = 401;
          ctx.body = {
              massage: 'token已过期，请重新登录',
              code: -1,
          }
          return;
      }
    } else {
      ctx.status = 401;
      ctx.body = {
        message: 'token不存在',
      };
      return;
    }
  }
}
```
接口先去header判断是否存在token，存在就验证，不存在就返回信息。


6、controller/login.js 登陆控制器 
```javascript
const Controller = require('egg').Controller;

class LoginController extends Controller {
  async login() {
    const { ctx, app } = this;
    const { userName, password } = ctx.request.body;
    const { result } = await ctx.service.login.login(userName, password);

    if(result.length>0){
      
      const token = app.jwt.sign({
        'userName': userName, //需要存储的 token 数据
      }, app.config.jwt.secret, { expiresIn: '2m' }); // 2分钟token过期
      
      ctx.set({'token':token})//设置headers
      ctx.body = {
        code: '200',
        data: [{
          token: token,
          userName: result[0].userName,
          userId: result[0].userId
        }],
        message: 'success'
      };

    }else{

      ctx.body = {
        code: '401',
        data: result,
        message: '账号或密码错误'
      };

    }
  }
}

module.exports = LoginController;
```
将用户名信息存储在token数据中，并且设置2分钟token过期。当token和用户名密码验证成功，返回token和用户信息。


7、service/login.js 
```javascript
const Service = require('egg').Service;
const crypto = require('crypto');

class LoginService extends Service {
  getMd5Data(data) {
    return crypto.createHash('md5').update(data).digest('hex');
  }

  async login(username,password) {
    const psd = this.getMd5Data(password)
    const data = await this.app.mysql.query(`SELECT * FROM user WHERE userName='${username}' and password='${psd}'`);
    const result = JSON.parse( JSON.stringify(data)); // 去除node中mysql查询数据产生的RowDataPacket
    return { result };
  }
}

module.exports = LoginService;
```
md5加密后查询匹配数据库密码。
# 验证
postman调用 post请求 填写用户名和密码获取token
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/egg3.png)
通过拿到的token放入header请求用户信息
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/egg4.png)
token不存在的情况
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/egg5.png)


token过期的情况
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/egg6.png)


账号密码错误的情况
![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/egg7.png)
