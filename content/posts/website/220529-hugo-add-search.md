---
title: "Hugo PaperMod 添加搜索功能"
date: 2022-05-29
draft: false
isCJKLanguage: true
tags: ["hugo"]
categories: ["website"]
---


[PaperMod](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-features/#search-page) 已集成 Fuse.js 实现搜索功能，本篇记录下如何使用

> 1. 新建页面
```
hugo new search.md
```
> 2. 编辑内容

{{< filename-highlight filename="./content/search.md" >}}
---
title: "搜索"
layout: "search"
---
{{</ filename-highlight >}}

> 3. 修改网站配置：添加菜单和参数

{{< filename-highlight lang="yml" filename="./config.yml" >}}
menu:
    main:
        - name: 搜索
          url: search/
          weight: 10
params:
    # 参考 https://fusejs.io/api/options.html
    fuseOpts:
        isCaseSensitive: false
        includeMatches: true
        shouldSort: true
        location: 0
        distance: 1000
        threshold: 0.4
        minMatchCharLength: 0
        keys: ["title", "permalink", "summary", "content"]
{{</ filename-highlight >}}
