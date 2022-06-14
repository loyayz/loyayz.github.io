---
title: "PaperMod 添加归档页面"
date: 2022-06-05
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
---

**需求**：添加归档页面显示所有文章

暂无自定义要求，直接使用 PaperMod 默认页面，这里记下使用步骤和注意事项

> 1. 新建页面
```shell
hugo new archives.md
```
```yml { title="./content/archives.md" }
---
title: "归档"
layout: "archives"
---
```
> 2. 修改网站配置
```yml { title="./config.yml" }
# 添加菜单
menu:
    main:
        - name: 归档
          url: archives/
params:
    # Hugo 默认只把 ./content/ 目录下有最多文章的目录设为主目录
    # 详情：https://gohugo.io/functions/where/#mainsections
    # 归档页面默认只显示主目录内的文章，如果有多个目录的话，需要修改这个配置
    mainSections:
        - coding
        - website          
```
