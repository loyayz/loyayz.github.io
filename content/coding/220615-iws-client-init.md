---
title: "IdeaWorlds  初始化前端工程"
date: 2022-06-15
draft: false
isCJKLanguage: true
tags: ["IdeaWorlds", "Angular"]
series: ["IdeaWorlds 前端开发笔记"]
---

IdeaWorlds 前端开发笔记（一）

《**IdeaWorlds 前端开发笔记**》记录从初始化到部署的过程，前端选用的`Angular`在国内使用人较少，希望本系列文章能帮助到初识该框架的开发，也希望未来会有同伴参与本项目。

## 1. 项目说明

[IdeaWorlds](https://ideaworlds.info) 是一个创意分享平台，并将提供一系列快速实现软件系统相关创意的服务。

- 前端选型
  - 框架：[Angular](https://angular.cn/)
  - 组件库：[NG-ZORRO](https://ng.ant.design/)

## 2. 搭建本地开发环境
- 安装 Node.js

  Angular 需要 Node.js 和 npm 包管理器，请先参阅 [nodejs.org](https://nodejs.org) 安装他们。

- 安装 Angular CLI 

  打开终端输入如下命令：
  ```shell
  npm install -g @angular/cli@v13
  ```
  >Angular 开发一般使用 Angular CLI 来创建项目，生成应用和库代码，以及执行各种持续开发任务，比如测试、打包和部署。
  >
  >写本篇时`Angular 14`刚发布不久，我选用的`NG-ZORRO`还没有适配，为避免可能的问题这里先指定安装`v13`，后续等适配后再升级到最新版。

## 3. 创建工作区

打开终端输入如下命令，将在当前目录创建`idea-worlds`文件夹，并在该文件夹内生成配置文件
```shell
ng n idea-worlds --create-application false
```
> 说明：
> 
> `ng n idea-worlds` 创建一个 Angular 新工作空间，并将工程命名为 idea-worlds
> 
> `--create-application false` 创建空工作空间，因为我计划在同一个工程中放多个应用程序

## 4. 初始化应用

目前我计划在同一个工程中放两个应用（展示前台、管理后台）和一个公共库

```shell
cd idea-worlds
ng g application www --routing --skip-tests --style less
ng g application console --routing --skip-tests --style less
ng g library libs --prefix iws
```
> 说明：
>
> `ng g application www` 创建一个名为 www 的应用
> 
> `ng g application console` 创建一个名为 console 的应用
> 
> `ng g library libs` 创建一个名为 libs 的公共库
>
> `--routing` 创建一个路由
>
> `--skip-tests` 不生成测试文件
> 
> `--style less` 指定样式文件的拓展名，因为`NG-ZORRO`使用`less`，保持一致避免出错
> 
> `--prefix iws` 指定库组件的前缀

## 5. 初始化 NG-ZORRO

```shell
ng add ng-zorro-antd --project www
ng add ng-zorro-antd --project console
```
> 说明：
> 
> 为`www`和`console`两个应用添加`NG-ZORRO`组件库
> 
> 执行命令时会有交互选择，多语言选`zh_CN`，其他直接按`Enter`键
> 

## 6. 启动应用

- 启动`www`应用
  ```shell
  ng serve --project www --open
  ```
- 启动`console`应用
  ```shell
  ng serve --project console --open
  ```

  > 说明：
  >
  > `ng serve` 命令会启动开发服务器、监视文件，并在这些文件发生更改时重建应用
  >
  > `--open` 选项会自动打开你的浏览器，并访问 http://localhost:4200/
  > 
  > `--project` 指定要启动的应用，可以用`ng serve --open`启动`www`，因为他是默认应用
