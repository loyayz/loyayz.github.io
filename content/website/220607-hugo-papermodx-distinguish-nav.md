---
title: "PaperMod 区分列表和文章页的导航文字"
date: 2022-06-07
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

**需求**：PaperMod 列表和文章页的导航文字都是`上一页/下一页`，这在中文表述中不太准确，因此将文章页的文字改为`上一篇/下一篇`

> 1. 编辑 i18n
```yml { title=“./i18n/zh.yaml” }
# 加入这几行
- id: prev_post
  translation: "上一篇"

- id: next_post
  translation: "下一篇"
```
> 2. 编辑页面导航模板
```html { title="./layouts/partials/post_nav_links.html" }
{{ i18n "prev_page" }} 改为 {{ i18n "prev_post" }}
{{ i18n "next_page" }} 改为 {{ i18n "next_post" }}
```
