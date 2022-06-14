---
title: "PaperMod 列表文章标题后添加标识"
date: 2022-06-03
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
series: ["PaperModx 定制列表页"]
---

**需求**：自动为列表中的文章添加对应的标识：`[置顶]`、`[转载]`

> 1. 将`./themes/PaperMod/layouts/_default/list.html`复制到`./layouts/_default/list.html`

```html { title="./layouts/_default/list.html" linenostart=66 }
  <header class="entry-header">
    <h2>
      {{- .Title }}
      <!-- 加入下面这行，表示 weight 值为 1 的文章标题后添加 [置顶] 标识 -->
      {{- if (eq .Weight 1) }}<sup><span class="x-entry-istop">&nbsp;&nbsp;[置顶]</span></sup>{{- end }}
      <!-- 加入下面这行，表示 outer 值为 true 的文章标题后添加 [转载] 标识 -->
      {{- if (.Param "outer") }}<sup><span class="x-entry-isouter">&nbsp;&nbsp;[转载]</span></sup>{{- end }}
      {{- if .Draft }}<sup><span class="entry-isdraft">&nbsp;&nbsp;[draft]</span></sup>{{- end }}
    </h2>
  </header>
```

> 2. 将`./themes/PaperMod/layouts/_default/archives.html`复制到`./layouts/_default/archives.html`

```html { title="./layouts/_default/list.html" linenostart=31 }
        <h3 class="archive-entry-title">
          {{- .Title | markdownify }}
          <!-- 归档页面显示 [置顶] 标识怪怪的，所以这里没添加 -->
          <!-- 加入下面这行，表示 outer 值为 true 的文章标题后添加 [转载] 标识 -->
          {{- if (.Param "outer") }}<sup><span class="x-entry-isouter">&nbsp;&nbsp;[转载]</span></sup>{{- end }}
          {{- if .Draft }}<sup><span class="entry-isdraft">&nbsp;&nbsp;[draft]</span></sup>{{- end }}
        </h3>
```

> 3. 修改样式
```css { title="./assets/_css/extended/custom.css" }
.x-entry-isouter {
  font-size: 14px;
  color: #cd201f;
}

.x-entry-isouter {
  font-size: 14px;
  color: #2db7f5;
}
```

> 4. 使用姿势：在文章页面修改配置来开关标识
```yml
---
title: "测试"
date: 2022-06-03
draft: false
# 1 表示置顶
weight: 1
# true 表示是转载
outer: true
---
```
