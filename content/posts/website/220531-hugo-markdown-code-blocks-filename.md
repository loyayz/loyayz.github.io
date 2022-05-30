---
title: "Hugo Markdown 代码块显示文件名"
date: 2022-05-31
draft: false
isCJKLanguage: true
tags: ["hugo", "markdown"]
categories: ["website"]
---

通过 [Hugo Shortcode Templates](https://gohugo.io/templates/shortcode-templates/) 包装
[内置的 highlight](https://github.com/gohugoio/hugo/blob/master/tpl/tplimpl/embedded/templates/shortcodes/highlight.html) 实现此功能

> 1. 新建模板

{{< filename-highlight lang="html" filename="./layouts/shortcodes/filename-highlight.html" >}}
{{ $filename := .Get "filename" }}
{{ $lang := .Get "lang" }}
{{ $options := .Get "options" }}
{{ $input := (trim .Inner "\n\r") }}

<div class="filename-highlight-wrapper">
    <div class="filename-highlight-title">
        <strong>文件：</strong><span>{{ $filename }}</span>
    </div>
    {{ highlight $input $lang $options }}
</div>
{{</ filename-highlight >}}

> 2. 添加样式：该文件是 PaperMod 的拓展样式文件，其他主题请查看对应的方式

{{< filename-highlight lang="css" filename="./assets/css/extended/custom.css" >}}
.filename-highlight-wrapper .highlight {
  margin-top: 0 !important;
  border-top-left-radius: 0 !important;
  border-top-right-radius: 0 !important;
}
/* --hljs-bg 和 --radius 是 PaperMod 的变量 */
.filename-highlight-title {
  background: var(--hljs-bg) !important;
  border-top-left-radius: var(--radius);
  border-top-right-radius: var(--radius);
  color: #e7ce56;
  padding: 4px 0 0 16px;
  font-size: .78em;
}
.filename-highlight-title span {
  color: #d5d5d6;
}
{{</ filename-highlight >}}

> 3. 使用方式：
```
{{ < filename-highlight lang="javascript" filename="文件名" >}}
 因为模板会自动解析，所以这里 {{ 和 < 之间有个空格，使用的时候记得删除
 最终效果看上面两个代码块
{{ </ filename-highlight >}}
```
