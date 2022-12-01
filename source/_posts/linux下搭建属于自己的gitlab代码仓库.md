---
title: linux下搭建属于自己的gitlab代码仓库
date: 2022-12-01 13:30
tags:
---

## 准备工作
1.首先去阿里云或者腾讯云购买一台Linux云服务器(至少2核4g以上配置centos系统，否则会卡顿，gitlab比较吃内存)，资金不够的可以先按量付费。
2.买完服务器后在安全组下开放几个端口，如:80、8443、22、443、8080、8022等按实际情况来开放端口号。
<!-- more -->

## 开始
### 一、连接服务器
用任何终端工具连接服务器，或者可以使用在线版终端工具[webssh](https://github.com/VANTERC/webssh) 

### 二、安装docker环境
[docker官网安装文档](https://docs.docker.com/engine/install/centos/)
直接复制下面4行命令即可
```bash
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo systemctl start docker
```

### 三、docker安装gitlab
先在home目录下创建5个文件夹
```bash
sudo mkdir -p /mnt/gitlab/etc      //配置 
sudo mkdir -p /mnt/gitlab/log      //日志 
sudo mkdir -p /mnt/gitlab/data     //数据 
sudo mkdir -p /mnt/gitlab/backups  //备份
sudo mkdir -p /mnt/gitlab/etc/ssl  //ssl证书
sudo chmod 700 /mnt/gitlab/etc/ssl //目录授权
```

这里用服务器8443映射gitabl容器443端口，8080映射80端口，8022映射22端口
```bash
docker run -d -p 8443:443 -p 8080:80 -p 8022:22 --privileged --name gitlab --restart always --volume /home/gitlab/etc:/etc/gitlab:Z --volume /home/gitlab/log:/var/log/gitlab:Z --volume /home/gitlab/data:/var/opt/gitlab:Z --volume /home/gitlab/backups:/var/opt/gitlab/backups:Z beginor/gitlab-ce:11.3.0-ce.0
```

### 四、访问gitalb

直接打开浏览器访问服务器ip+端口号就可以了，启动需要一定时间，多刷新几次就行。
如果长时间没有访问成功，就执行docker ps -a 查看重启是否正常启动。
默认用户名为root，密码请自己手动重置。

### 五、配置gitlab

```
gitlab.rb为配置文件，邮件配置等
vim /home/gitlab/etc/gitlab.rb
```

```
gitlab.yml同为配置文件配置克隆域名地址等
vim /mnt/gitlab/data/gitlab-rails/etc/gitlab.yml
将host的值改成映射的外部主机ip地址和端口 
host: gitlab.vanterc.com 
port: 8090 https: false
```

```
docker命令：
docker ps -a 查看所有容器
docker exec -ti gitlab /bin/bash 进入容器 
exit 退出容器 
docker stop 15dc7a4e144e 停止容器
docker rm 15dc7a4e144e 删除容器
docker restart 15dc7a4e144e 重启容器
```

```
gitlab容器内命令：
gitlab-ctl restart 重启配置
gitlab-ctl reconfigure 应用配置
```
```
配置https
把证书文件上传至外部挂载目录
/home/gitlab/etc/ssl
```

```
查看gitlab启动日志
docker logs gitlab > ./gitlab.log 
```

```
邮件配置的一些注意点：
显示当前使用的邮件发送协议（要smtp） ActionMailer::Base.delivery_method
查看当前的邮件发送配置 ActionMailer::Base.smtp_settings
测试邮件 Notify.test_email('123456789@qq.com', '邮件标题', '邮件正文').deliver_now
```

备份数据库
gitlab-rake gitlab:backup:create 该命令会备份gitlab仓库、数据库、用户、用户组、用户密钥、权限等信息。 默认在/home/gitlab/backups



