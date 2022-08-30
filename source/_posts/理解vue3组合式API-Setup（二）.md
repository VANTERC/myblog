---
title: 理解vue3组合式API  Setup（二）
date: 2021-07-05 14:27
tags:
---
### Props
![carbon 11.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(11)_1625540082237.png)
创建一个子组件，在子组件内监听父组件计数器。
子组件props只能通过toRefs进行解构，否则组件将无法响应式传递参数。
如果content是可选的，在未传入content的情况下，toRefs是不会为它创建ref，所以需要使用toRef来处理。
### Context
![carbon 12.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(12)_1625541122747.png)
context就显而易见了，这个参数能访问到之前vue2上this可访问到的三个属性，原因setup在创建时，dom还未被创建，所以this访问不到，通过上下文的方式来传递这三个属性:
``` js
this.$attrs  
this.$slots  
this.$emit 
```

### reactive
![carbon 14.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(14)_1625542582030.png)

reactive其实和toRef类似，都是将数据变为响应式，不同是reactive一般用来处理复杂对象数据之类。
### 转换写法
![carbon 16.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(16)_1625555312208.png)
将setup移到script标签内，这样整个script内就都是setup的天下，引入defineProps定义属性，此时就不需要再将变量方法通过return来返回出去了，然后我们再通过reactive定义一个user对象，让setUserAge按钮来修改对象内age值，点击按钮来查看ui的更新变化。
### 子组件调用父组件方法
![carbon 18.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(18)_1625558127998.png)
在父组件内定义fatherClick方法并且有个age参数，在子组件内引入defineEmit，通过defineEmit(['fatherClick']) 来触发父组件方法并传递参数，如果是多个方法，在此数组里['fatherClick']添加即可。
![carbon 19.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/carbon%20(19)_1625558863142.png)
同样我们也可以通过context来调用父组件方法，但是需要引入useContext函数。
#### 总结
vue3的新特性还有很多，同时也在不断更新优化，当前官方部分api并不全面，许多问题在官网也查询不到，所以不建议用在生产环境。组合式api还有大量要学习的地方，对于vue2是一种延续又是一种大变革；有一定vue开发经验的同学可能上手相对好一点，但对于新手，vue3的变化不是很友好，很多特征的理解相比2来说更加有深度，但是不代表vue3不好，一门语言的发展，势必要取其精华，去其糟粕，在性能和优化上vue3确实比vue2更上一个阶梯，包括新的构建工具vite，但是学习的成本也是在的，拥抱ts，也让vue不再像之前那么友好新手了，但是ts势必是个大趋势，未来的大环境，所以还是好好学习，与时俱进吧！