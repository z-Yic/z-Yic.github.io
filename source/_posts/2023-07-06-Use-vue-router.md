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

### 问题1-根路由指向

<mark>**根路由是否应该直接指向`App.vue`**</mark>？

`App.vue`在项目挂载时会直接渲染到`index.html`中id为`app`的元素中，而根路由`/`是所有路由的顶层路由，且是项目网站的根目录，将根路由`/`的component字段直接指向`App.vue`理论上是合理的；

但是如果这样做了，根路由的路由出口`<router-view>`放哪呢？也放在`App.vue`中就显得不合理了！那么就不设置路由出口，发现确实可行，那么索性把根路由的component字段都省略了不更好，删除根路由的component字段后，发现控制台有警告信息-没设置根路由；

显然将<mark>根路由直接指向`App.vue`并不合理</mark>，而且<mark>官方的示例代码中并没发现将根由指向`App.vue`的类似作法</mark>，而是专门创建了一个专门显示首页内容的组件，让根路由指向该组件；

所以推荐作法是：将根路由的`<router-view>`放置在`App.vue`中，但是根路由的`component`字段指向专门显示首页内容的组件(比如一个布局组件)；

### 问题2-视图位置

<mark>**`<router-link>`和`<router-view>`必须位于同一个组件内吗**</mark>?

答案是<mark>二者可以放置在不同的组件内</mark>，[文档](https://router.vuejs.org/zh/guide/#router-view)中对`<router-view>`的介绍是：<u>"`router-view` 将显示与 url 对应的组件。你可以把它放在<mark>任何地方</mark>，以适应你的布局"</u>；

一开始对<mark>“任何地方”</mark>的理解局限为`<router-link>`所在组件的任意地方，但事实上<mark>“任何地方”</mark>除了组件内任意地方，还可以将`<router-view>`放在与`router-link`不同的组件中；以上的示例代码可以发现：`视图1->视图2`(`<router-link to="/view2">`)位于视图1中，而`<router-view is="view2">`位于`App.vue`中，点击该路由链接后能正常显示视图2；

现在想了一下：很多模板类语法，本质都是代码复用，那么对于单页应用，所有的代码最终都在一个页面中，`<router-link>`和`<router-view>`最终都会转换成`index.html`中的元素：点击`<router-link>`后，会查找路由表，将匹配到的路由项中指定的组件在对应的`<router-view>`处渲染；联系单页应用的概念来理解刷新了自己以往的认知；

### 问题3-命名视图

<mark>**`component`字段不能指定命名视图**</mark>？

默认情况下，`component`指定的组件都是在非命名视图`<router-view>`中渲染，能否在给`component`赋值时使用命名视图呢？实际测试发现，<mark>命名视图必须配合`components`字段使用，`component`字段不直接命名视图</mark>；

<mark>**使用命名视图时，不匹配的视图会被重置**</mark>？

案例中点击视图1中的`视图1->视图2`的路由链接后，视图2直接覆盖了视图1的内容，为什么不是视图1和视图2同时显示呢？在首页中明明设置了`<router-view name="view1">`和`<router-view name="view2">`，且是并列放置的。

要想视图1和视图2同时显示，需修改`/view2`的路由为`{path: '/view2' components: {view1: view1, view2: view2}}`，即视图1的的组件也一同传入；所以<mark>使用命名视图时，未匹配的视图会被重置，如果要显示之前显示过的命名视图必须重传</mark>；

### 问题4-嵌套路由

<mark>**嵌套路由的`<router-view>`只能在顶层路由中**</mark>？

因为案例中没有使用嵌套路由，所以以上案例不能发现该问题；但在调整项目结构过程中发现，与问题2中`<router-view>`可以在任意位置的结论不同，使用嵌套路由(使用了`children`且嵌套路由不以`/`开头)时，`<router-view>`必须设置在顶层路由指向的组件中。上升到单页应用代码在一起来理解，使用嵌套路由后，顶层路由所对应的组件形成了一个独立的组件空间，嵌套路由指定的组件只能在该独立空间被渲染，不能穿透到独立空间外；

将嵌套路由以通过完整url的方式设置路由时，不存在该问题，所以<mark>`children>`形式的嵌套路由的视图只能设置在顶层路由对应的组件中</mark>；

