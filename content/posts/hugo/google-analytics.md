---
title: "Hugo 集成 Google Analytics"
date: 2022-05-29T01:00:00+08:00
draft: false
tags: ["hugo", "blog"]
categories: ["hugo"]
---

[Hugo 内部模板](https://gohugo.io/templates/internal) 已集成 Google Analytics，本篇记录下如何使用

## 1 创建 Google Analytics

[Google Analytics 官网文档](https://support.google.com/analytics/answer/9306384?hl=zh-Hans&ref_topic=9303319)

> 1. 登录 [Google Analytics](https://analytics.google.com/) 点击首页的`开始衡量`按钮
> 2. 按页面提示创建账号，创建成功后会自动跳转至管理页
> 3. 按页面提示设置数据流，设置成功后会自动跳转至详情页
> 4. 在数据流详情页找到衡量ID

## 2 Hugo 配置

> 1. 编辑网站根目录下的`config.yml`
```
googleAnalytics: 衡量ID
```
> 2. PaperMod 主题启用内部模板：`layouts/partials/extend_head.html`
```
{{- if not .Site.IsServer -}}
{{ template "_internal/google_analytics.html" . }}
{{- end }}
```
