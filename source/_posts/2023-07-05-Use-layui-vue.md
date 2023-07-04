---
title: Use layui-vue
date: 2023-07-05 00:05:30
tags:
- vue
- layui-vue
---
为了简化vue项目的开发，通常会直接在项目中引用第三方组件库中的组件。就个人(小白)了解到的有[element-plus](https://element-plus.org/)，[ant design](https://www.antdv.com/)，[layui-vue](http://www.layui-vue.com);

综合个人能力和源码风格的考虑，优先选择了使用`layui-vue`来适应组件库开发，[layui-vue文档](http://www.layui-vue.com/zh-CN/guide/introduce)；

## 安装layui-vue

```bash
npm install @layui/layui-vue --save
```

## 按需引用组件

因为全局引入`layui-vue`会一次性加载所有组件，推荐通过插件按需引用；

1. 安装自动引用插件；

   ```bash
   npm install -D unplugin-vue-components unplugin-auto-import
   ```

2. 修改`vite.config.json`；

   ```javascript
   import { fileURLToPath, URL } from 'node:url'
   
   import { defineConfig } from 'vite'
   import vue from '@vitejs/plugin-vue'
   
   import AutoImport from 'unplugin-auto-import/vite'
   import Components from 'unplugin-vue-components/vite'
   import { LayuiVueResolver } from 'unplugin-vue-components/resolvers'
   
   // https://vitejs.dev/config/
   export default defineConfig({
     plugins: [
       vue(),
       AutoImport({
         resolvers: [LayuiVueResolver()],
       }),
       Components({
         resolvers: [LayuiVueResolver()],
       }),
     ],
     resolve: {
       alias: {
         '@': fileURLToPath(new URL('./src', import.meta.url))
       }
     }
   })
   ```

## 按需引用所需组件

1. 查找所需组件，[layui-vue组件](http://www.layui-vue.com/zh-CN/components/color)；

   ![](layui-vue-轮播图组件.png)

2. 查看源码，按需复制；

   ```vue
   <template>
     <lay-carousel v-model="active1">
       <lay-carousel-item id="1">
         <div style="color: white;text-align: center;width:100%;height:300px;line-height:300px;background-color:#009688;">条目一</div>
       </lay-carousel-item>
       <lay-carousel-item id="2">
         <div style="color: white;text-align: center;width:100%;height:300px;line-height:300px;background-color:#5FB878;">条目二</div>
       </lay-carousel-item>
       <lay-carousel-item id="3">
         <div style="color: white;text-align: center;width:100%;height:300px;line-height:300px;background-color:#FFB800;">条目三</div>
       </lay-carousel-item>
   </template>
   ```

3. 查看组件api文档，按需修改源码；

   ![](layui-vue-轮播图组件api.png)

   ```vue
   <script setup>
   import { ref } from 'vue'
   
   const active = ref("1")
   </script>
    
   <template>
       <lay-carousel v-model="active" autoplay="true">
           <lay-carousel-item id="1">
               <img src="../assets/images/桌面1.png"/>
           </lay-carousel-item>
           <lay-carousel-item id="2">
               <img src="../assets/images/桌面2.png" />
           </lay-carousel-item>
           <lay-carousel-item id="3">
               <img src="../assets/images/桌面3.png" />
           </lay-carousel-item>
       </lay-carousel>
   </template>
   
   <style>
   img {
       width: 100%;
       height: 100%;
       object-fit: cover;
   }
   </style>
   ```

4. 效果预览

   ![](layui-vue-轮播图效果展示.png)

## 补充

- 对于组件库的使用，不要求熟记组件源码，即查即用即可；
- 选项式api的源码，调整成组合式api需要自己手动完成；
- 组件样式的修改调整，要求掌握相关CSS知识；
- 因为版本问题(包括vue，node，layui-vue版本)，可能导致有些组件的源码复制到本地无法使用，这时建议查看文档说明，比如：源码中可通过`<lay-icon></lay-icon>`调用内置图标，但本地使用时却发现识别不了`<lay-icon>`标签，后发现[组件图标](http://www.layui-vue.com/zh-CN/components/icon)的方式才引用图标成功；

