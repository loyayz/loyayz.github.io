---
title: "Hugo 代码块显示标题"
date: 2022-05-31
draft: false
isCJKLanguage: true
tags: ["hugo", "markdown"]
categories: ["website"]
---

通过 [Markdown Render Hooks | Hugo](https://gohugo.io/templates/render-hooks/) 实现此功能

## 1 新建模板

```html { title="./layouts/_default/_markup/render-codeblock.html" }
{{- $lang := .Type | default "text" }}
{{- $title := (split (.Attributes.title | default "") ";") }}
{{- $titleText := (trim (index $title 0 | default "") " ") }}
{{- $titleShowName := (trim (index $title 1 | default "true") " ") }}
{{- $titleName := (trim (index $title 2 | default "文件：") " ") }}
<div class="custom-highlight-wrapper">
{{- if gt (len $titleText) 0 }}
    <div class="custom-highlight-title">
        {{- if eq $titleShowName "true" }}
        <strong>{{- $titleName }}</strong>
        {{- end }}
        {{ $titleText }}
    </div>
{{- end }}
{{ highlight .Inner $lang .Options }}
</div>
```

## 2 添加样式

```css { title="./assets/css/extended/custom.css" }
/* 该文件是 PaperMod 的拓展样式文件，其他主题请查看对应的方式 */

.custom-highlight-wrapper :nth-child(2) {
  margin-top: 0 !important;
  border-top-left-radius: 0 !important;
  border-top-right-radius: 0 !important;
}

/* --hljs-bg 和 --radius 是 PaperMod 主题提供的变量 */
.custom-highlight-title {
  background: var(--hljs-bg) !important;
  border-top-left-radius: var(--radius);
  border-top-right-radius: var(--radius);
  color: #e7ce56;
  padding: 4px 0 0 16px;
  font-size: .78em;
}
```

## 3 使用方式

代码块加上 title 属性，因为模板会自动解析，所以示例 ``` 前有个 | ，使用时得删除

```html { title="我是文件名" }
| ```html { title="我是文件名" }
  <p>我是个 html</p>
| ```
```

```markdown { title="我只是个标题;false" }
| ```markdown { title="我只是个标题;false" }
  false 表示不在标题前加前缀
| ```
```

```text { title="我有新前缀;true;我是：" }
| ```text { title="我有新前缀;true;我是：" }
  第三个参数是前缀名，默认值 “文件：”
| ```
```
