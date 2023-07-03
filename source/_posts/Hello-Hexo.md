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
- 方式1：在`source`目录中创建images文件夹，并将文章所要引用的图片存储在这；markdown引用图片时使用`绝对路径`，根目录为`source`所在的目录；
  - 这种方式适合引用图片较少的情形；因为如果将所有文章的图片都在存储在该文件夹中，文件夹体积会过大，且不方便查找；
  - markdown语法示例：`![/images/1.png]`
- 方式2：修改`_config.yml`中`wtriting`部分`post_asset_folder: true`，此时通过`hexo new <title>`新建文章，会同时在`_posts`目录中生成与文章同名的文件夹，可以使用该文件夹来存放该文章所引用的图片，markdown引用图片时使用`相对路径`；
  - 这种方式因为每篇文章都有一个对应的文件夹用于存放图片，所以适合多图片情形；
  - markdown语法示例：`![](./pageName/1.png)`;
  - 可能存在的问题：
    1. 因为每篇文章对应一个文件夹，如果某天新建的标题与之前的一致，那么会创建同名的副本文章和副本文件夹，虽然不影响使用，但更推荐修改新建post文章的命名方式为`new_post_name: :year-:month-:day-:title.md`，这样文章名会在标题前加上日期，增加区分度；
    2. 虽然文章详情页图片加载正常，但在归档页和首页该图片可能加载失败；解决方法是使用相应的插件语法，而不能使用markdown引用图片语法；
- 方式3：同样修改`_config.yml`中`wtriting`部分，并添加markdown支持，修改如下；markdown引用图片时直接使用对应目录中的`文件名`；
  - 这种方式是方式2的改进版，在规定页和首页也支持markdown引用图片语法，而且引用图片的`相对路径`可以直接省去文件夹名部分，使用起来更加便捷；
  - markdown语法示例：`![](1.png)`;
  ```yml
  post_asset_folder: true
  marked:
    prependRoot: true
    postAsset: true
  ```
- 引用图片的详细说明，建议查看官方文档[资源文件夹](https://hexo.io/zh-cn/docs/asset-folders);
<mark>补充</mark>：如果文件名中含有中文，使用vscode自动补全文件名时，会将中文转换为对应unicode码值，但该码值无法正常获取文件，必须手动改回中文文件名才能正常获取；