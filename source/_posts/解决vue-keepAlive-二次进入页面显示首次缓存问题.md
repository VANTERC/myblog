---
title: 解决vue keepAlive 二次进入页面显示首次缓存问题
date: 2021-07-26 14:04
tags:
---
问题场景：
当某个带有筛选条件查询列表的页面需要进行缓存，以便不再需要重复进行选择或者输入筛选条件的时候，我们就可以利用keepAlive来进行缓存，但keepAlive也存在着一些坑，这是需要注意的地方。
<!-- more -->
### 如何利用keepAlive进行缓存
1、在路由meta内定义keepAlive，来设置需要被缓存的页面
```js
meta: { keepAlive: true }
```
ture: 需要缓存的路由；false:不需要缓存的路由

2、判断router-view
```js
<keep-alive>
  <router-view v-if="$route.meta.keepAlive"/>
</keep-alive>
<router-view v-if="!$route.meta.keepAlive"></router-view>
```
被keep-alive包裹的为需要缓存的页面，这样我们就可以通过keepAlive来切换哪些页面需要缓存，哪些不需要缓存。

3、进入详情页面缓存，否则不缓存
```js
beforeRouteLeave (to, from, next) {
    if (to.path === '/index/page1/detail') {
      from.meta.keepAlive = true
      next()
    } else {
      from.meta.keepAlive = false
      next()
    }
  }
```
路由守卫钩子 beforeRouteLeave 离开页面路由的时候出发；
当离开缓存页面，进入详情页面的时候，我们将缓存页面keepAlive设置true，如果离开缓存页面，不是进入详情页面的时候，我们设置为false，不进行缓存。

这是最基本的缓存设置，但是keepAlive也留下了一个大坑。

## keepAlive二次进入页面显示首次缓存问题
第一次从缓存页面进入详情页再返回到缓存页面的时候，页面条件能被正确的缓存下来，但是当我们切换到别的路由，再一次进入该缓存页修改查询条件，并进入详情后返回缓存页，此时缓存的是页面状态是第一次进入该页面的时候的状态，也就是说，第二次进入页面的时候，该缓存读取的是第一次的缓存，这就非常的坑了。
所以通过下面的刷新缓存路由的方式来解决这个问题。

1、调整 router-view
```js
// view
<keep-alive v-if="isRouterAlive">
  <router-view v-if="$route.meta.keepAlive"/>
</keep-alive>
<router-view v-if="!$route.meta.keepAlive"></router-view>

// js
data () {
  return {
    isRouterAlive: true
  }
},
provide () { return { reload: this.reload } },

methods: {
  reload () {
    this.isRouterAlive = false
    this.$nextTick(() => (this.isRouterAlive = true))
  }
}
```

定义一个isRouterAlive变量，用来刷新keep-alive;通过provide向下面子孙组件们暴露一个reload方法，用来刷新缓存。

2、设置缓存
```js
export default {
 inject: ['reload'],
 data () {
   return {
    ....
   },
 },
 beforeRouteEnter (to, from, next) {
    to.meta.keepAlive = true
    if (from.path !== '/index/page1/detail') {
      next(vm => {
        vm.reload()
      })
    } else {
      next()
    }
  },
  beforeRouteLeave (to, from, next) {
    if (to.path === '/index/page1/detail') {
      from.meta.keepAlive = true
      next()
    } else {
      from.meta.keepAlive = false
      next()
    }
  }
}
```

子组件注册inject: ['reload'] 方法，beforeRouteEnter钩子在进入缓存页面的时候通过通过判断路由是否是来自详情页面，如果不是详情页，我们就刷新缓存，如果是详情页，就不做处理。
这样就解决了，只要是路由进入过别的页面(非详情页面)，我们就刷新缓存，这样就不会出现二次进入的时候，显示的是第一次的缓存状态，同时从详情页返回后正常显示缓存数据。

