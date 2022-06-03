---
title: "PaperMod 自定义页面底部信息"
date: 2022-06-04
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

**需求**：自定义页面底部信息，添加`版权说明`和`关于我`两个页面链接

> 1. 将`./themes/PaperMod/layouts/partials/footer.html` 复制到`./layouts/partials/footer.html`
> 2. 新建自定义底部信息模板
```html { title="./layouts/partials/footer_info.html" }
<!-- 将 footer.html 中 <footer> 的代码移至 footer_info.html -->
<footer class="footer">
    {{- if site.Copyright }}
    <span>{{ site.Copyright | markdownify }}</span>
    {{- else }}
    <span>&copy; {{ now.Year }} <a href="{{ "" | absLangURL }}">{{ site.Title }}</a></span>
    {{- end }}
    <span>
        Powered by
        <a href="https://gohugo.io/" rel="noopener noreferrer" target="_blank">Hugo</a> &
        <a href="https://github.com/loyayz/hugo-PaperModx/" rel="noopener" target="_blank">PaperModx</a>
    </span>
</footer>
```
> 4. 编辑模板
```html { title="./layouts/partials/footer.html" }
<!-- 添加引用 footer_info.html -->
{{- if not (.Param "hideFooter") }}
{{- partial "footer_info.html" . }}
{{- end }}
```
```html { title="./layouts/partials/footer_info.html" }
<footer class="footer">
    <span>&copy; {{ now.Year }} <a href="/about/">loyayz</a></span>
    <span>
        Powered by
        <a href="https://gohugo.io/" rel="noopener noreferrer" target="_blank">Hugo</a> &
        <a href="https://github.com/loyayz/hugo-PaperModx/" rel="noopener" target="_blank">PaperModx</a>
    </span>
    <span>·&nbsp;<a href="/copyright/">版权说明</a></span>
</footer>
```
注：可以直接修改`./layouts/partials/footer.html`，我是为了方便后续修改而这样拆分
