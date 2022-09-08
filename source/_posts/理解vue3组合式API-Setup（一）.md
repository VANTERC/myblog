---
title: 理解vue3组合式API  Setup（一）
date: 2021-06-25 16:18
tags:
---
[vue3.0](https://v3.cn.vuejs.org/)发布也有一段时间了，用了那么久vue，3.0相比较2.0最大的区别就在于引入了-----组合式API。
尝试通过尤大最新发布的前端构建工具[Vite](https://cn.vitejs.dev/)(下一代前端编译构建工具)，新建了一个vue3.0的初始项目，很快就发现了与2.0的不同之处如下图：
<!-- more -->
![carbon 2.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(2)_1624615490869.png)
#### [Setup](https://v3.cn.vuejs.org/guide/composition-api-introduction.html#%E4%BB%80%E4%B9%88%E6%98%AF%E7%BB%84%E5%90%88%E5%BC%8F-api)  
可以看到在script标签旁边多了setup，那么具体是做什么作用呢？
首先我们要知道尤大引入组合式api的目的是什么？
**目的是为了解决复杂业务下代码逻辑的碎片化，以便于更好的阅读和维护。**
![carbon 1.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(1)_1624615396489.png)
我们现将setup理解为一个用来存放你逻辑代码的地方,并且它有2个参数props和context
需要注意的一点是：
setup在执行时，组件实例尚未被创建，setup 选项中是不存在this的
### 在生命周期钩子中调用
![carbon 4.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(4)_1625454437233.png)
在setup中定义一个方法并将其return，然后就可以在mounted中调用，只有return出的变量或者函数，才可以在setup外部被this调用。
![carbon 5.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(5)_1625454662080.png)
同样我们也可以这么写，将生命周期函数放入setup中调用，前提是必须注册生命周期方法onMounted
选项 API 生命周期选项和组合式 API 之间的映射，详情参加官方api
- ~~beforeCreate~~ -> use setup()

- ~~created~~ -> use setup()

- beforeMount -> onBeforeMount

- mounted -> onMounted

- beforeUpdate -> onBeforeUpdate

- updated -> onUpdated

- beforeUnmount -> onBeforeUnmount

- unmounted -> onUnmounted

- errorCaptured -> onErrorCaptured

- renderTracked -> onRenderTracked

- renderTriggered -> onRenderTriggered

- activated -> onActivated

- deactivated -> onDeactivated

这里要说的一点是setup内，相当于之前的created钩子，也就是说是最先执行的
下面来引入一个新的函数
### [ref](https://v3.cn.vuejs.org/guide/composition-api-introduction.html#%E5%B8%A6-ref-%E7%9A%84%E5%93%8D%E5%BA%94%E5%BC%8F%E5%8F%98%E9%87%8F)
![carbon 7.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(7)_1625455356513.png)
ref 函数使任何响应式变量在任何地方起作用
### watch监听
![carbon 8.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(8)_1625456544045.png)
我们用ref来定义一个计数器变量，在setup中用watch来监听计数变化
### computed 计算属性
![carbon 9.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(9)_1625464411330.png)
计算属性twiceTheCounter，需要像ref 一样使用 .value 来获取值
