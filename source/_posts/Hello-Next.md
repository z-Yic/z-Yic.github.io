---
title: Hello Next
date: 2023-07-03 20:49:49
tags:
- hexo
- next
---
## 说明
`hexo`可以使用主题来装饰个人网站，项目默认使用自带主题(theme: landscape)，用户可以在项目的`/thems`中自定义主题，不过这需具备一定的前端功底，所以通常会直接使用第三方主题；

官方提供了自己的[主题库](https://hexo.io/themes)，当然也可以去代码平台进行搜索，比如[github](https://github.com);

本文选择了[hexo-theme-next](https://github.com/theme-next/hexo-theme-next);

## 克隆主题到本地
切换到hexo项目的temems目录下，将next项目下载在该目录中，next项目所在目录为`hexo-theme-next`：
```bash
git clone https://github.com/theme-next/hexo-theme-next.git
```

## 使用next主题
修改hexo项目根目录中`_config.yml`文件，以使用`next`主题;
```yml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: hexo-theme-next
```

## 按需修改主题样式
下载`next`后，可以直接对`themes/hexo-theme-nex/_config.yml`进行修改，以显示不同的样式；
例如：
next的默认scheme为“Muse”
```yml
# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------

# Schemes
scheme: Muse
# scheme: Mist
# scheme: Pisces
# scheme: Gemini
```
![Muse](next-scheme-Muse.png)
换用“Gemini”
```yml
# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------

# Schemes
# scheme: Muse
# scheme: Mist
# scheme: Pisces
scheme: Gemini
```
![Gemini](next-scheme-Gemini.png)
