---
title: "IdeaWorlds 特性模块"
date: 2022-06-20
draft: false
isCJKLanguage: true
tags: ["IdeaWorlds", "Angular"]
series: ["IdeaWorlds 前端开发笔记"]
---

IdeaWorlds 前端开发笔记（五）

**本篇目标**：创建特性模块。

1. 新建模块

   创建`home、idea、news、people、tag`五个模块并使用[懒加载](https://angular.cn/guide/lazy-loading-ngmodules)。
   ```shell
   ng g module features/home --route home --module app.module --project www
   ng g module features/idea --route idea --module app.module --project www
   ng g module features/news --route news --module app.module --project www
   ng g module features/people --route people --module app.module --project www
   ng g module features/tag --route tag --module app.module --project www
   ```
2. 修改组件路径

   懒加载模块自动创建的组件路径不符[项目规范](/coding/220616-iws-client-project-structure)，需要手动调整下，以`idea`为例：
   - 在`idea/`目录下创建文件夹`components/`
   - 在`idea/components/`目录下创建文件夹`idea/`
   - 将`idea.component.(html/less/ts)`三个文件移至`idea/components/idea/`下
   - 修改`idea.module.ts`和`idea-routing.module.ts`内的引用路径
      - 即`./idea.component`改为`./components/idea/idea.component`
   
   最终文件结构如下：
   ```text
   ├─src/app/features
      ├─home
      │  │  home-routing.module.ts
      │  │  home.module.ts
      │  │
      │  └─components
      │      └─home
      │              home.component.html
      │              home.component.less
      │              home.component.ts
      │
      ├─idea
      │  │  idea-routing.module.ts
      │  │  idea.module.ts
      │  │
      │  └─components
      │      └─idea
      │              idea.component.html
      │              idea.component.less
      │              idea.component.ts
      │
      ├─news
      │  │  news-routing.module.ts
      │  │  news.module.ts
      │  │
      │  └─components
      │      └─news
      │              news.component.html
      │              news.component.less
      │              news.component.ts
      │
      ├─people
      │  │  people-routing.module.ts
      │  │  people.module.ts
      │  │
      │  └─components
      │      └─people
      │              people.component.html
      │              people.component.less
      │              people.component.ts
      │
      └─tag
         │  tag-routing.module.ts
         │  tag.module.ts
         │
         └─components
             └─tag
                     tag.component.html
                     tag.component.less
                     tag.component.ts
   ```

3. 修改路由

   ```typescript { title="src/app/app-routing.module.ts" }
   import { NgModule } from '@angular/core';
   import { PreloadAllModules, RouterModule, Routes } from '@angular/router';
   
   const routes: Routes = [
     { path: 'idea', loadChildren: () => import('./features/idea/idea.module').then(m => m.IdeaModule) },
     { path: 'news', loadChildren: () => import('./features/news/news.module').then(m => m.NewsModule) },
     { path: 'people', loadChildren: () => import('./features/people/people.module').then(m => m.PeopleModule) },
     { path: 'tag', loadChildren: () => import('./features/tag/tag.module').then(m => m.TagModule) },
     { path: '', loadChildren: () => import('./features/home/home.module').then(m => m.HomeModule), pathMatch: 'full' },
     { path: '**', redirectTo: '/' },
   ];
   
   @NgModule({
     imports: [RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules })],
     exports: [RouterModule]
   })
   export class AppRoutingModule {
   }
   ```
