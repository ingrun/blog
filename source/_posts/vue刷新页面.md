---
title: vue 刷新当前页面
date: 2020-05-08
---



> https://blog.csdn.net/qq_16772725/article/details/80467492
> https://segmentfault.com/a/1190000017007631



## 简答粗暴

```js
// 这两条都能实现，强制刷新页面

location.reload()
this.$router.go(0)
```



## 移动到一个空白页

```html
//移动到一个空白页 再移动回来

this.$router.replace({path: "/back",  query: {url: '/thisUrl' }})

<template>
</template>

<script>
   export default {
      data() {
      },
      mounted(){
        var url = this.$route.query("url")
		this.$router.replace(url)
      },
  }
</script>
```



## provide / inject 组合

原理：允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效

在`App.vue`,声明`reload`方法，控制`router-view`的显示或隐藏，从而控制页面的再次加载。

```html
<template>
  <div id="app">
    <router-view v-if="isRouterAlive"></router-view>
  </div>
</template>

<script>
export default {
  name: 'App',
  provide () {
    return {
      reload: this.reload
    }
  },
  data () {
    return {
      isRouterAlive: true
    }
  },
  methods: {
    reload () {
      this.isRouterAlive = false
      this.$nextTick(function () {
        this.isRouterAlive = true
      })
    }
  }
}
</script>
```

在需要用到刷新的页面。在页面注入`App.vue`组件提供（`provide`）的 `reload` 依赖，在逻辑完成之后（删除或添加…）,直接`this.reload()`调用，即可刷新当前页面。

```html
<template>
</template>

<script>
   export default {
      data() {
      },
      inject: ["reload"],
      mounted(){

      },
       muthods(){
           reload(){
               this.reload()
           }
       }
  }
</script>
```



## 路由参数变化而页面不刷新

假如我们当前的路由为 ` /goods?id=1`，跳转后的路由为 `/goods?id=2`，这时因为vue 的`组件复用`机制， vue 是不会刷新页面的，我们可以监听路由变化，主动重新获取数据

```javascript
watch: {
    $route (to, from) {
		this.goodsId = this.$router.query.id 
        this.init()
    }
}

// 当然，强制刷新也是可以实现的 
```



