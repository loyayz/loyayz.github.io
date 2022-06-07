---
title: "Hugo 代码块显示标题"
date: 2022-05-31
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

通过 [Markdown Render Hooks | Hugo](https://gohugo.io/templates/render-hooks/) 实现此功能

## 1 新建模板

```html { title="./layouts/_default/_markup/render-codeblock.html" }
{{- $lang := .Type | default "text" }}
{{- $title := (split (.Attributes.title | default "") ";") }}
{{- $titleText := (trim (index $title 0 | default "") " ") }}
{{- $titleShowName := (trim (index $title 1 | default "true") " ") }}
{{- $titleName := (trim (index $title 2 | default "") " ") }}
<div class="x-highlight-wrapper">
{{- if gt (len $titleText) 0 }}
    <div class="x-highlight-title">
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
.x-highlight-wrapper :nth-child(2) {
  margin-top: 0 !important;
  border-top-left-radius: 0 !important;
  border-top-right-radius: 0 !important;
}

/* --hljs-bg 和 --radius 是 PaperMod 主题提供的变量 */
.x-highlight-title {
  background: var(--hljs-bg) !important;
  border-top-left-radius: var(--radius);
  border-top-right-radius: var(--radius);
  color: #e7ce56;
  padding: 4px 0 0 16px;
  font-size: .78em;
}
```

## 3 使用方式

\`\`\`html { title="我是文件名" }

\`\`\`

```html { title="我是文件名" }
  代码块加上 title 属性，使用时得加上代码块所属语言
```

\`\`\`markdown { title="我只是个标题;false" }

\`\`\`

```markdown { title="我只是个标题;false" }
第二个参数控制是否在标题前加前缀，默认值 "true"
```

\`\`\`text { title="loyayz;true;我是：" }

\`\`\`

```text { title="loyayz;true;我是：" }
第三个参数是前缀名，无默认值
```
