---
title: "PaperMod 统计列表文章篇数和字数"
date: 2022-06-10
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

**需求**：统计全站或列表中文章篇数和字数

实现步骤：

1. 新建统计模板
```html { title="./layouts/partials/stat.html" }
{{- $scratch := newScratch -}}

{{- $pages := union .RegularPages .Sections -}}
{{- if .IsHome -}}
{{- $pages = where site.RegularPages "Type" "in" site.Params.mainSections -}}
{{- end -}}

{{- range $pages -}}
{{- $scratch.Add "words" .WordCount -}}
{{- end -}}

{{- $posts := len $pages -}}
{{- $words := $scratch.Get "words" -}}
{{- printf "%s 篇 %s 字" ($posts | string) ($words | string) -}}
```

2. 使用姿势
```html
<!-- 在想要展示的地方加入这段代码 -->
{{- partial "stat.html" . }}
```

我是加在列表模板中，并可在`config.yml`添加参数`ShowStatInSectionTermList`控制是否展示
```html { title="./layouts/_default/list.html" linenostart=22 }
{{- if (.Param "ShowStatInSectionTermList" | default true) }}
<sup class="x-page-stat">{{- partial "stat.html" . }}</sup>
{{- end }}

<!-- 这行上面 -->
 </h1>
```

```css { title="./assets/css/extended/custom.css" }
.x-page-stat {
  color: var(--secondary);
  font-size: 14px;
}
```
