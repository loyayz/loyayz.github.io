---
title: "PaperModx 模板：统计文章篇数和字数"
date: 2022-06-11
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
series: ["PaperModx 定制列表页"]
---

**需求**：新增一个模板，用来统计文章篇数和字数，并展示在列表页和归档页

实现步骤：

1. 新建统计模板
```html { title="./layouts/partials/stat.html" }
{{- $scratch := newScratch -}}
{{- $kind := .Kind }}
{{- $pages := where site.RegularPages "Type" "in" site.Params.mainSections -}}

{{- if eq $kind "pages" -}}
{{-   $pages = .pages -}}
{{- else if or (eq $kind `term`) (eq $kind `section`) -}}
{{-   $pages = union .RegularPages .Sections -}}
{{- end -}}

{{- range $pages -}}
{{- $scratch.Add "words" .WordCount -}}
{{- end -}}

{{- $words := $scratch.Get "words" | default 0 }}
{{- printf "%s 篇 %s 字" ((len $pages) | string) ($words | string) -}}
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
