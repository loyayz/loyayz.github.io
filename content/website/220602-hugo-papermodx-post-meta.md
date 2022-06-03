---
title: "PaperMod 文章元数据前添加图标"
date: 2022-06-02
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

## 1 说明

**文章元数据**：文章的页面配置及一些[内置的变量](https://gohugo.io/variables/page/)
```yml
---
title: "PaperMod 文章元数据前添加图标"
date: 2022-06-02
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

页面配置：定义在 Hugo 文章源码头部的信息，比如上面这些就是本篇的页面配置
```

**需求**：本站使用 PaperMod 主题，文章元数据在两个地方，默认展示方式有点单调，因此想在原基础上给每个文本前添加图标
> 1. 文章详情页：正文上面头部标题和描述的下一行
> 2. 文章列表页：每个文章项的底部

下面是具体实现步骤，其他主题也可参考，只要修改对应的展示模板就行

## 2 修改网站配置
```yml { title="./config.yml" }
params:
    hideMeta: false
    ShowReadingTime: true
    ShowWordCount: true
```

## 3 新建元数据模板文件

```markdown { title="./layouts/partials/post_meta.html" }
{{ $scratch := newScratch }}

{{ if not .Date.IsZero }}
{{ $scratch.Add "meta_keys" (slice "date") }}
{{ $scratch.SetInMap "meta_items" "date" (slice "calendar" (.Date | time.Format (default "January 2, 2006" site.Params.DateFormat))) }}
{{ end }}

{{ if (.Param "ShowWordCount") }}
{{ $scratch.Add "meta_keys" (slice "words") }}
{{ $scratch.SetInMap "meta_items" "words" (slice "file-text" (i18n "words" .WordCount | default (printf "%d words" .WordCount))) }}
{{ end }}

{{ if (.Param "ShowReadingTime") }}
{{ $scratch.Add "meta_keys" (slice "read_time") }}
{{ $scratch.SetInMap "meta_items" "read_time" (slice "clock" (i18n "read_time" .ReadingTime | default (printf "%d min" .ReadingTime))) }}
{{ end }}

{{ with (partial "author.html" .) }}
{{ $scratch.Add "meta_keys" (slice "author") }}
{{ $scratch.SetInMap "meta_items" "author" (slice "avatar" .)}}
{{ end }}

{{ $metaItems := $scratch.Get "meta_items" }}
{{ range ($scratch.Get "meta_keys") }}
{{ $icon := (partial "extend_svg.html" (index $metaItems . 0)) }}
{{ $text := (printf "<span>%s</span>" (index $metaItems . 1)) }}
{{ $scratch.Add "meta" (slice (printf "<span class=\"x-post-meta-item\">%s%s</span>" $icon $text )) }}
{{ end }}

{{ with ($scratch.Get "meta") }}
{{ delimit . "&nbsp;·&nbsp;" }}
{{ end }}
```

<details>
<summary>点此查看本步骤的说明</summary>

通过查看两个页面源码（文章详情页`themes/PaperMod/layouts/_default/single.html`、文章列表页`themes/PaperMod/layouts/_default/list.html`）可发现它们引用了同一个模板展示文章元数据`{{- partial "post_meta.html" . -}}`

查阅 [Partial Templates | Hugo](https://gohugo.io/templates/partials) 可知，可以在网站根目录新建模板文件覆盖主题模板
> Partial templates—like single page templates and list page templates—have a specific lookup order. However, partials are simpler in that Hugo will only check in two places:
> 
> 1. layouts/partials/*<PARTIALNAME>.html
> 2. themes/<THEME>/layouts/partials/*<PARTIALNAME>.html
>
> This allows a theme’s end user to copy a partial’s contents into a file of the same name for further customization.
</details>

## 4 新建图标模板文件

```html { title="./layouts/partials/extend_svg.html" }
{{ $icon_name := . }}
{{ if (eq $icon_name "") }}
{{ else if (eq $icon_name "calendar") }}
<svg> ... </svg>
{{ else if (eq $icon_name "file-text") }}
<svg> ... </svg>
{{ else if (eq $icon_name "clock") }}
<svg> ... </svg>
{{ else if (eq $icon_name "avatar") }}
<svg> ... </svg>
{{ else if (eq $icon_name "tag") }}
<svg> ... </svg>
{{ else }}
{{ partial "svg.html" (dict "name" $icon_name) }}
{{ end }}
```

为省略篇幅这里`<svg> ... </svg>`没写内容，[可点此查看 PaperModx 完整内容](https://github.com/loyayz/hugo-PaperModx/blob/master/layouts/partials/svg.html)

## 5 添加样式

```css { title="./assets/css/extended/custom.css" }
.x-post-meta-item {
  display: inline-block;
}

.x-post-meta-item svg {
  width: 1em;
  height: 1em;
  display: inline-block;
  vertical-align: -0.15em;
  margin-right: 4px;
}
```
