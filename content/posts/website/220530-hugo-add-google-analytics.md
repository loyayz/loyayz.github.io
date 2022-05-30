---
title: "Hugo 添加谷歌分析"
date: 2022-05-30
draft: false
isCJKLanguage: true
tags: ["hugo"]
categories: ["website"]
---

[Hugo 内部模板](https://gohugo.io/templates/internal) 已集成 Google Analytics，本篇记录下如何使用

> 1. 登录 [Google Analytics](https://analytics.google.com/) 点击首页的`开始衡量`按钮
> 2. 按页面提示创建账号，创建成功后会自动跳转至管理页
> 3. 按页面提示设置数据流，设置成功后会自动跳转至详情页
> 4. 在数据流详情页找到`衡量ID`
> 5. 修改网站配置

{{< filename-highlight lang="yml" filename="./config.yml" >}}
googleAnalytics: 衡量ID
privacy:
    googleAnalytics:
        disable: false
{{</ filename-highlight >}}
