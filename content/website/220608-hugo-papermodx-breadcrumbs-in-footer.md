---
title: "PaperMod 文章页底部添加面包屑导航"
date: 2022-06-08
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
categories: ["website"]
---

**需求**：当文章比较长时无法点到面包屑，因此需要在文章页面的底部添加面包屑导航

1. 编辑文章页面模板
```html { title="./layouts/_default/single.html" linenostart=38 }
<footer class="post-footer">
    <!--  添加下面这行  -->
    {{ partial "breadcrumbs.html" . }}

    {{- if (.Param "ShowPostNavLinks") }}
    {{- partial "post_nav_links.html" . }}
    {{- end }}
</footer>
```

2. 添加样式
```css { title="./assets/css/extended/custom.css" }
/* 文章页面底部的面包屑居右 */
.post-footer > .breadcrumbs {
  justify-content: flex-end;
}
```
