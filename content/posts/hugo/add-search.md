---
title: "Hugo PaperMod 添加搜索功能"
date: 2022-05-30T00:00:00+08:00
draft: false
tags: ["hugo", "blog"]
categories: ["hugo"]
---


[PaperMod](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-features/#search-page) 已集成 Fuse 实现搜索功能，本篇记录下如何使用

> 1. 在网站根目录新建文件`content/search.md`
```
---
title: "搜索"
layout: "search"
---
```
> 2. 编辑网站根目录下的`config.yml`添加菜单和参数
```
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
```
