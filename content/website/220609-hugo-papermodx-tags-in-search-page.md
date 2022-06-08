---
title: "PaperMod 查询页展示标签列表"
date: 2022-06-09
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

**需求**：在查询页展示标签列表

**原因**：标签页（`/tags`）需要一个入口，但是 PaperMod 默认的标签页比较简洁，占用一个菜单感觉有点浪费，而搜索页面内提供标签列表似乎挺合理。

以下是实现步骤

1. 编辑搜索页面模板
```html { title="./layouts/_default/search.html" linenostart=28 }
{{- if not (.Param "hideTags")}}

<!-- 标签标题，可通过 search.md 中的 tagsTitle 修改 -->
<h2 style="margin-top: 32px">{{- (.Param "tagsTitle") | default "tags" }}</h2>
<ul class="terms-tags">
    {{- range $name, $value := .Site.Taxonomies.tags }}
    {{- $count := .Count }}
    {{- with site.GetPage (printf "/tags/%s" $name) }}
    <li>
        <a href="{{ .Permalink }}">{{ .Name }} <sup><strong><sup>{{ $count }}</sup></strong></sup> </a>
    </li>
    {{- end }}
    {{- end }}
</ul>
{{- end }}

<!-- 在最后一行前加入上面的代码 -->
{{- end }}{{/* end main */}}
```

2. 使用姿势

可直接使用，不需要修改吗，也可通过以下配置隐藏标签列表

```yml { title="./content/search.md" }
---
title: "搜索"
layout: "search"
# 是否隐藏标签
hideTags: true
tagsTitle: 标签 
---
```
