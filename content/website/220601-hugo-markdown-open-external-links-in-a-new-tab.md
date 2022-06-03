---
title: "Hugo 用新标签页打开外部链接"
date: 2022-06-01
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

通过 [Markdown Render Hooks | Hugo](https://gohugo.io/templates/render-hooks/) 实现此功能

```html { title="./layouts/_default/_markup/render-link.html" }
<a href="{{ .Destination | safeURL }}"{{ with .Title}} title="{{ . }}"{{ end }}{{ if strings.HasPrefix .Destination "http" }} target="_blank" rel="noopener nofollow noreferrer" {{ end }}>{{ .Text  | safeHTML }}</a>
```
