---
title: "Hugo Github Pages 网站搭建"
date: 2022-05-27
draft: false
isCJKLanguage: true
tags: ["hugo"]
categories: ["website"]
---

基于 [Hugo](https://gohugo.io) 搭建并部署在 [Github Pages](https://pages.github.com)

## 1 安装 Hugo

[官网教程](https://gohugo.io/getting-started/installing/)
> 1. 新建目录`C:\programs\hugo`
> 2. 在[官网](https://github.com/gohugoio/hugo/releases)下载最新版，本次下载 [hugo_extended_0.99.1_Windows-64bit.zip](https://github.com/gohugoio/hugo/releases/download/v0.99.1/hugo_extended_0.99.1_Windows-64bit.zip)
> 3. 解压 zip 文件，并将解压出的`hugo.exe`移到`C:\programs\hugo`
> 4. 添加环境变量：
*`win+R` -> `sysdm.cpl` -> 高级 -> 环境变量(N)... -> 系统变量(S) -> 双击 Path -> 新建 -> 输入`C:\programs\hugo`*
> 5. 打开命令行，输入`hugo version`，显示版本号即安装成功

## 2 创建网站
### 2.1 生成网站
> 1. 新建目录`C:\code\loyayz\loyayz.github.io`用于存放网站源码
> 2. 执行命令生成网站
> 3. 初始化 git 仓库，未安装 git 请先安装：[Git 官网](https://git-scm.com/)
```shell
cd C:\code\loyayz\loyayz.github.io
hugo new site . -f yml
git init
```

### 2.2 使用主题
> 1. 下载：在[官网](https://themes.gohugo.io/)挑选主题，本次使用 [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
```shell
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```
> 2. 修改网站配置：我是直接用[示例配置](https://github.com/adityatelange/hugo-PaperMod/blob/exampleSite/config.yml)覆盖后再修改

{{< filename-highlight lang="yml" filename="./config.yml" >}}
baseURL: "https://loyayz.github.io/"
title: loyayz
theme: PaperMod
paginate: 10

enableInlineShortcodes: true
enableRobotsTXT: true
enableEmoji: true
pygmentsUseClasses: true
hasCJKLanguage: true
defaultContentLanguage: zh
timeZone: "Asia/Shanghai"

buildDrafts: false
buildFuture: false
buildExpired: false

minify:
    disableXML: true
    # minifyOutput: true

languages:
    zh:
        languageName: "中文"
        weight: 1
        taxonomies:
            category: categories
            tag: tags

menu:
    main:
        - name: 文章
          url: posts/
          weight: 1
        - name: 分类
          url: categories/
          weight: 2
        - name: 标签
          url: tags/
          weight: 3

outputs:
    home:
        - HTML
        - RSS
        - JSON

params:
    env: production
    title: "loyayz's blog"
    description: "loyayz's blog - https://loyayz.com"
    author: loyayz
    images: ["papermod-cover.png"]
    keywords: [loyayz,ideaworlds,simpleframework]
    DateFormat: "2006-01-02"
    ShowReadingTime: false
    ShowShareButtons: false
    ShowCodeCopyButtons: true
    ShowFullTextinRSS: false
    defaultTheme: auto
    disableThemeToggle: false
    disableSpecial1stPost: true
    disableScrollToTop: false
    disableAnchoredHeadings: false
    hideMeta: false
    hideSummary: false
    showtoc: true
    tocopen: false
    ShowPostNavLinks: true
    ShowBreadCrumbs: true
    ShowWordCount: false
    ShowRssButtonInSectionTermList: false
    comments: false
    hideFooter: true
    
    #homeInfoParams:
    #    Title: ""
    #    Content: 
    socialIcons:
        - name: github
          url: "https://github.com/loyayz"
        - name: email
          url: "mailto:loyayz@foxmail.com"
        - name: RsS
          url: "index.xml"        

markup:
    goldmark:
        renderer:
            unsafe: true
    highlight:
        codeFences: true
        guessSyntax: true
        lineNos: true
        style: monokai

privacy:
    disqus:
        disable: true
    googleAnalytics:
        disable: true
    instagram:
        disable: true
    twitter:
        disable: true
    vimeo:
        disable: true
    youtube:
        disable: true
{{</ filename-highlight >}}

> 3. 拓展：如下新建拓展文件`custom.css`、`extend_head.html`、`extend_footer.html`
```
.(loyayz.github.io)
├── config.yml
├── assets/
│   └── css/
│       └── extended/
│          └── custom.css
└── layouts
    └── partials
        ├── extend_footer.html
        └── extend_head.html
```

{{< filename-highlight lang="css" filename="./assets/css/extended/custom.css" >}}
/* 修改代码块的最大高度 */
.highlight table {
  max-height: 400px
}
{{</ filename-highlight >}}

### 2.3 本地调试

命令行内执行命令后，在浏览器打开`http://localhost:1313`可预览网站
```shell
hugo server -D
```

## 3 部署到 Github Pages
### 3.1 新建脚本
  - Windows 一键提交脚本

{{< filename-highlight lang="shell" filename="./deploy.bat" >}}
@echo off
git add .
git commit -m "update"
git push
{{</ filename-highlight >}}

  - Linux 一键提交脚本

{{< filename-highlight lang="shell" filename="./deploy.sh" >}}
#!/bin/sh
git add .
git commit -m "update"
git push
{{</ filename-highlight >}}

  - GitHub Action 自动化部署脚本

{{< filename-highlight lang="shell" filename="./.github/workflows/gh-pages.yml" >}}
name: github pages

on:
  push:
    branches:
      - master
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
          fetch-depth: 1

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/master'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
{{</ filename-highlight >}}

### 3.2 创建远程仓库
> 1. 创建远程仓库：打开 [GitHub](https://github.com/new) 创建仓库，仓库名为`loyayz.github.io`其他都不选
> 2. 新建`.gitignore`文件避免误操作提交文件

{{< filename-highlight filename="./.gitignore" >}}
public/
.idea/*
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
{{</ filename-highlight >}}

> 3. 关联本地和远程仓库，提交并推送到 Github
```
git add .
git commit -m "init"
git branch -M master
git remote add origin https://github.com/loyayz/loyayz.github.io.git
git push --set-upstream origin master
```
> 4. 新建仓库说明文档

{{< filename-highlight lang="markdown" filename="./README.md" >}}
本仓库使用 git submodule 引用主题仓库，因此 git clone 本仓库后需要再执行下句命令

git submodule update --init --recursive --depth=1

部署：双击`deploy.bat`或执行命令`sh deploy.sh`
{{</ filename-highlight >}}

> 5. 双击`deploy.bat`推送到 Github 触发自动化部署
### 3.3 修改 Github Pages

`GitHub Action`默认生成网站到`gh-pages`分支，因此需要修改`Github Pages`来源
> 1. 浏览器打开远程仓库，进入设置页面：`Settings -> Pages`
> 2. 将Source 分支中的`master`换成`gh-pages`
> 3. 浏览器打开链接`https://loyayz.github.io`即可看到网站

## 4 创建文章
> 1. 执行命令行启动服务，浏览器打开`http://localhost:1313`预览网站
```
hugo server -D
```
> 2. 打开另一个命令行执行命令创建文章，网站将实时显示新的文章
```
hugo new posts/website/hugo-init.md
```
> 3. 编辑文章

{{< filename-highlight filename="./content/posts/website/hugo-init.md" >}}
---
title: "Hugo Github Pages 网站搭建"
date: 2022-05-27
draft: false
isCJKLanguage: true
tags: ["hugo"]
categories: ["website"]
---

基于 [Hugo](https://gohugo.io) 搭建并部署在 [Github Pages](https://pages.github.com)
{{</ filename-highlight >}}

注意：`draft`需改为`false`表示该文章不是草稿，否则推送到远程仓库后不会部署该文章
> 4. 双击`deploy.bat`部署，稍等几秒后刷新`https://loyayz.github.io`即可看到网站新内容
