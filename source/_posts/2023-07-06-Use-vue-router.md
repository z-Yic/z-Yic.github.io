---
title: Use vue-router
date: 2023-07-06 03:14:21
tags:
- vue
- vue-router
---

[vue-router](https://router.vuejs.org/zh/)是[vue](https://cn.vuejs.org/)的官方路由，主要用于单页应用中通过路由动态渲染组件(使用[动态组件](https://cn.vuejs.org/guide/essentials/component-basics.html#dynamic-components)也可以实现该效果，不过需要额外编写判断逻辑)；

- [vue-router官网](https://router.vuejs.org/)
- [vue-router文档](https://router.vuejs.org/zh/introduction.html)

## 安装vue-router

```bash
npm install vue-router@4
```

## 案例描述

3个组件：首页，view1， view2；

- 首页组件：包含了3个路由链接以及2个命名视图；
- view1组件：视图1的文本内容，1个跳转到view2的路由链接；
- view2组件：视图2的文本内容，1个跳转到首页的路由链接；

## 案例实现

### 编写模板文件

```vue
<!--src/App.vue-->
<template>
  <router-link to="/" active-class="clicked">首页</router-link> |
  <router-link to="/view1" active-class="clicked">视图1</router-link> |
  <router-link to="/view2" active-class="clicked">视图2</router-link>
  <div>
    <router-view name="view1" />
    <router-view name="view2" />
  </div>
</template>

<style scoped>
.clicked {
  color: aqua;
}
</style>
```

```vue
<!--src/view/view1.vue-->
<template>
    这是视图1
    <router-link to="/view2">视图1->视图2</router-link>
</template>
```

```vue
<!--src/view/view2.vue-->
<template>
    这是视图2
    <router-link to="/">视图2->首页</router-link>
</template>
```

### 注册路由

```javascript
<!--src/router/index.js-->
import { createRouter, createWebHashHistory } from "vue-router"
import App from '../App.vue'
import view1 from '../views/view1.vue'
import view2 from '../views/view2.vue'

const routes = [
    {
        path: '/', 
        component: App
    },
    {
        path: '/view1',
        components: {
            view1: view1
        }
    },
    {
        path: '/view2',
        components: {
            view2: view2
        }
    }
]

const router = createRouter({
    history: createWebHashHistory(),
    routes: routes
})

export default router
```

```javascript
<!--main.js-->
import { createApp } from 'vue'
import App from './App.vue'
import router from './router/index'

const app = createApp(App)
app.use(router)
app.mount('#app')
```

### 效果预览

![首页](首页视图.png)

![视图1](视图1.png)

![视图2](视图2.png)

## 问题思考

其实以上案例与[官方文档的入门案例](https://router.vuejs.org/zh/guide/)在实现方式和功能上有些不同，为什么要用自己写的案例呢？主要是自己项目经验不足，还没有形成定式思维，对于路由的设置有一些自己的想法，所以按照自己的想法进行了构建。自己的想法是否合理？虽然最终效果符合预期，但实际上存在着很多问题，一只菜鸟的个人猜测显然比不过技术团队的智慧结晶；当然暴露问题，也说明自己相关知识掌握的并不熟练，没有理解底层原理；

### 根路由是否应该直接指向`App.vue`？

**个人猜测**

`App.vue`在项目挂载时会直接渲染到`index.html`中id为`app`的元素中，而根路由`/`是所有路由的顶层路由，且是项目网站的根目录，将根路由`/`的component字段直接指向`App.vue`理论上是合理的；

**验证猜测**

但是如果这样做了，根路由的路由出口`<router-view>`放哪呢？也放在`App.vue`中就显得不合理了！那么就不设置路由出口(注册一个假路由)，发现确实可行，那么索性把根路由的component字段都省略了不更好，删除根路由的component字段后，发现控制台有警告信息-没设置根路由；

显然将<mark>根路由直接指向`App.vue`并不合理</mark>，而且<mark>官方的示例代码中并没发现将根由指向`App.vue`的类似作法</mark>，而是专门创建了一个专门显示首页内容的组件，让根路由指向该组件；

**个人结论**

所以推荐作法是：将根路由的`<router-view>`放置在`App.vue`中，但是根路由的`component`字段指向专门显示首页内容的组件(比如一个布局组件)；

### <del>`<router-link>`和`<router-view>`必须位于同一个组件内吗?</del>

**个人猜测**

<del>答案是<mark>二者可以放置在不同的组件内</mark></del>，[文档](https://router.vuejs.org/zh/guide/#router-view)中对`<router-view>`的介绍是：<u>"`router-view` 将显示与 url 对应的组件。你可以把它放在<mark>任何地方</mark>，以适应你的布局"</u>；

**验证猜测**

一开始对<mark>“任何地方”</mark>的为：`<router-link>`所在组件的任意地方，如示例中：`<router-link to="/view1">`和`<router-view name="view1">`都在`App.vue`中；但事实上<mark>“任何地方”</mark>除了组件内任意地方，还可以将`<router-view>`放在与`router-link`不同的组件中，如示例代码中：`视图1->视图2`(`<router-link to="/view2">`)位于视图1中，而`<router-view is="view2">`位于`App.vue`中，点击该链接后能正常显示视图2；

**猜测错误**

<del>所以<mark>`<router-view>`可以放在任意组件的任意地方</mark></del>？我原以为这就是最终结论，但我错了：

- 当我把`App.vue`中`<router-view name="view2">`移动到`view1.vue`中，点击`视图2`和`视图1->视图2`，发现视图2没显示出来；
- 同样的我将`<router-view2 name="view2">`移到了`view2.vue`中，也无法显示视图2的内容；
- 只有放在`App.vue`中才能正常显示；

**新的猜测与验证**

显然我之前的理解是错误的，加上后面使用嵌套路由，我发现这与路由有关：`/view2`是一个根路径(而不是嵌套路由中的相对路径，而且示例中没有使用嵌套路由)，点击`<router-link to="/view2">`后，会直接去`App.vue`(这是项目的根组件)中找路由出口；

以上的案例之所以在我的错误理解下也运行成功了，是因为凑巧所有路由出口都放在了`App.vue`中，一旦把路由出口放到其它组件中，自然找不到路由出口了，导致显示失败，所以<mark>默认情况下，所有`<router-view>`必须放在`App.vue`中</mark>！

上面之所以说`默认情况`，是因为在使用嵌套路由时，我遇到不同的问题：

1. 我把嵌套路由的路由出口也放在了`App.vue`中，但发现组件没有被渲染出来；
2. 我将嵌套路由的路由出口设置在顶层路由的组件中，才正常渲染；
3. 我将嵌套路由抽离出来(<mark>不通过children来设置嵌套路由</mark>)，使用嵌套路由的绝对路径来设置路由，发现嵌套路由可以在`App.vue`中渲染；

**个人结论**

所以问题本身就是建立在一个错误的理解之上，根本就不应该存在`<router-link>`和`<router-view>`是否应该在同一组件的假设，因为<mark>`<router-view>`的位置是有硬性要求的</mark>;

1. 当<mark>链接不指向嵌套路由时，`<router-view>`必须放在`App.vue`中</mark>。点击非指向嵌套路由的链接后，会先查找路由表，找到要渲染的组件，然后去`App.vue`中找对应的路由出口，然后将组件在该位置渲染；
2. 当<mark>链接指向一个嵌套路由时，`<router-view>`必须放在其顶层路由对应的组件中</mark>。点击指向嵌套路由的链接后，会先在`App.vue`中渲染顶层路由指定的组件，然后将嵌套路指定的组件在顶层路由组件中的路由出口处渲染；

### `component`字段能使用命名视图赋值吗？

默认情况下，`component`指定的组件都是在非命名视图`<router-view>`中渲染，能否在给`component`赋值时使用命名视图呢？答案是不能；<mark>`component`字段不能使用命名视图赋值，且命名视图必须配合`components`字段使用</mark>；

## 吐槽

就算本人较菜，也不应该看文档都不会使用vue-router吧；尤其是`<router-view>`是否必须放在`App.vue`的问题，文档只是简单一句`<router-view>`可以放在任意地方，这种有歧义甚至误导性的说辞，让一个菜鸟怎么去理解。

关于框架的学习和使用，优秀的框架确实可以提升开发效率，避免重复造轮子，毕竟自己造出来的轮子可能跑都跑不了！但并不意味着学好框架就行，一方面舍弃自己的思维方式去迎合框架的设计，最终会形成某种定式思维，另外使用框架的过程中遇到问题，会依赖官方来解决问题，而不是自己尝试从源码中分析原因，去优化代码；所以<mark>使用框架前，先把基本功练扎实才是王道</mark>！
