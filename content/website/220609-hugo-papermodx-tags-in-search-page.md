---
title: "PaperMod 搜索页展示标签列表"
date: 2022-06-09
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
series: ["PaperModx 定制搜索页"]
---

**需求**：在搜索页展示标签列表

**原因**：标签页（`/tags`）需要一个入口，但是 PaperMod 默认的标签页比较简洁，占用一个菜单感觉有点浪费，而搜索页面内提供标签列表似乎挺合理。

以下是实现步骤

1. 编辑搜索页面模板
```html { title="./layouts/_default/search.html" linenostart=28 }
{{- if not (.Param "hideTags") }}
{{- $taxonomies := .Site.Taxonomies.tags }}
{{- if gt (len $taxonomies) 0 }}
<h2 style="margin-top: 32px">{{- (.Param "tagsTitle") | default "tags" }}</h2>
<ul class="terms-tags">
    {{- range $name, $value := $taxonomies }}
    {{- $count := .Count }}
    {{- with site.GetPage (printf "/tags/%s" $name) }}
    <li>
        <a href="{{ .Permalink }}">{{ .Name }} <sup><strong><sup>{{ $count }}</sup></strong></sup> </a>
    </li>
    {{- end }}
    {{- end }}
</ul>
{{- end }}
{{- end }}
{{- end }}{{/* end main */}}  <!-- 在最后一行前加入上面的代码 -->
```

2. 使用姿势
```yml { title="./config.yml" }
# 设置文章分类 https://gohugo.io/content-management/taxonomies
taxonomies:
    tag: tags
    series: series
```

设置文章所属标签，比如本篇是这样：
```yml
---
title: "PaperMod 搜索页展示标签列表"
date: 2022-06-09
draft: false
tags: ["hugo", "paper-modx"]
series: ["PaperModx 定制搜索页"]
---
```

可通过以下配置隐藏标签列表
```yml { title="./content/search.md" }
---
title: "搜索"
layout: "search"
# 是否隐藏标签
hideTags: true
tagsTitle: 标签 
---
```
