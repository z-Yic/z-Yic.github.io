---
title: Hello Hexo
date: 2023-07-03 20:03:33
tags:
---
## 安装hexo
hexo是基于node的网址构建工具，通常会借助npm安装hexo；
```bash
npm install -g hexo
```

## 初始化项目
会在当前目录中新建一个存放网址项目的目录，且已完成初始化；
```bash
hexo init [foler]
```

## 添加文章
默认在项目的`source/_posts`目录中生成对应`.md`文件；
```bash
hexo new <title>
```
<mark>注意</mark>：title会被用作文件名(`title.md`)，如果title中含有空格，应该使用引号包裹，文件名会使用`-`拼接；

## 启动项目
默认在本地的4000端口启动hexo服务，使用默认主题，可以查看到`_posts`目录中的文件；
```bash
npm run server
```

## markdown引用图片的问题
- 方式1：在`source`目录中创建images文件夹，存入要引用的图片；markdown引用图片时使用`绝对路径`，根目录为`source`所在的目录；这种方式引用图片较少的情形，因为所有文章都从该目录引用图片；
- 方式2：修改`_config.yml`中`wtriting`部分`post_asset_folder: true`，此时通过`hexo new`新建文章，会同时在`_posts`目录中生成同名的文件夹，使用该文件夹来存放该文章所用引用的图片，markdown引用图片时使用`相对路径`；这种方式每篇文章都有一个对应的文件夹用于存放图片，所以图片较多时查找起来更加方便，但这种方式可能在归档页和首页图片加载失败，这是markdown引用图片的问题，解决方法是使用相应的插件语法，而不能使用markdown引用图片语法；
- 方式3：修改`_config.yml`中`wtriting`部分，修改如下，markdown引用图片时使用`相对路径`；相较于方式2，支持markdown引用图片语法；
```yml
post_asset_folder: true
marked:
  prependRoot: true
  postAsset: true
  ```
- 引用图片的详细说明，建议查看官方文档[资源文件夹](https://hexo.io/zh-cn/docs/asset-folders);