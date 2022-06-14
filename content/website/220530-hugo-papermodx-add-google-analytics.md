---
title: "Hugo 添加谷歌分析"
date: 2022-05-30
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
---

[Hugo 内部模板](https://gohugo.io/templates/internal) 已集成 Google Analytics，本篇记录下如何使用

## 1 注册 Google Analytics
> 1. 登录 [Google Analytics](https://analytics.google.com/) 点击首页的`开始衡量`按钮
> 2. 按页面提示创建账号，创建成功后会自动跳转至管理页
> 3. 按页面提示设置数据流，设置成功后会自动跳转至详情页
> 4. 在数据流详情页找到`衡量ID`

## 2 修改网站配置
```yml { title="./config.yml" }
googleAnalytics: 衡量ID
privacy:
    googleAnalytics:
        disable: false
```

## 3 使用模板

在`<head>  </head>`中添加下面代码
```
{{ if not .Site.IsServer }}
{{ template "_internal/google_analytics.html" . }}
{{ end }}
```
**22/06/03 updated：PaperModx 是使用这种方式，可以不改下面两个配置**

注：PaperMod 默认已添加模板，但是是通过判断是否生产环境来启用模板的，这样会导致本地调试的时候也会启用，可以通过以下步骤修改
> 1. 修改网站配置
> ```yml { title="./config.yml" }
> params:
>     env: dev
> ```
>
> 2. 修改部署脚本：`run: hugo --minify`后加上`-e production`
> ```yml { title="./.github/workflows/gh-pages.yml }
>       - name: Build
>         run: hugo --minify -e production
> ```
