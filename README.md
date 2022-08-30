# VANTERC的个人博客
### 如何搭建hexo博客并部署至github？
#### 1.本地安装Hexo
```
npm install -g hexo-cli
```
#### 2.初始化博客项目
```
hexo init myblog
```
#### 3.修改配置文件_config.yml
```
deploy:
  type: git
  repo: https://github.com/VANTERC/myblog.git
  branch: deploy
```
_config.yml内其他参数可以安装自己的需求自己配置
deploy为要部署的分支，切记要和项目分支分开，这里我把main分支为博客主分支，deploy为部署分支
#### 4.将myblog项目推送到github仓库
```
git init
git remote add origin https://github.com/VANTERC/myblog.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

同时在github仓库Settings-Pages设置build分支为deploy分支

#### 5.打包部署项目
```
npm run clean
npm run build
npm run deploy
```
部署完成后就可以在Settings-Pages内看到访问地址了
https://vanterc.github.io/myblog/





