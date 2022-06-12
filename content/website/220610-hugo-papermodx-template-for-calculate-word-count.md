---
title: "PaperModx 模板：统计文章篇数和字数"
date: 2022-06-10
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

**需求**：新增一个模板，用来统计文章篇数和字数，并展示在列表页和归档页

实现步骤：

1. 新建统计模板
```html { title="./layouts/partials/stat.html" }
{{- $scratch := newScratch -}}

{{- if eq .Kind "pages" -}}
{{- $scratch.Add "pages" .pages -}}
{{- else -}}
{{-   $pages := union .RegularPages .Sections -}}
{{-   if .IsHome -}}
{{-     $pages = where site.RegularPages "Type" "in" site.Params.mainSections -}}
{{-   end -}}
{{-   $scratch.Add "pages" $pages -}}
{{- end -}}

{{- $pages := $scratch.Get "pages" -}}
{{- range $pages -}}
{{- $scratch.Add "words" .WordCount -}}
{{- end -}}
{{- printf "%s 篇 %s 字" ((len $pages) | string) (($scratch.Get "words") | string) -}}
```

2. 编辑模板
```html { title="./layouts/_default/list.html" linenostart=22 }
<!-- 列表模板-->

  {{- if (.Param "ShowStatInSectionTermList" | default true) }}
  <sup class="archive-count">&nbsp;&nbsp;{{- partial "stat.html" . }}</sup>
  {{- end }}
<!-- h1 这行上面 -->
</h1>
```
```html { title="./layouts/_default/archives.html" linenostart=20 }
<!-- 归档模板-->

  <h2 class="archive-year-header">
    {{- replace .Key "0001" "" }}
    {{- if (site.Params.ShowStatInArchive | default true) }}
    <sup class="archive-count">&nbsp;&nbsp;{{- partial "stat.html" (dict "Kind" "pages" "pages" .Pages) }}</sup>
    {{- else }}
    <sup class="archive-count">&nbsp;&nbsp;{{ len .Pages }}</sup>
    {{- end }}
  </h2>
```

3. 使用姿势

可直接使用，不需要修改，也可通过以下配置隐藏统计

```yml { title="./config.yml" }
params:
    ShowStatInSectionTermList: false
    ShowStatInArchive: false
```
