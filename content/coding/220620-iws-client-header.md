---
title: "IdeaWorlds 网站页头"
date: 2022-06-20
draft: false
isCJKLanguage: true
tags: ["IdeaWorlds", "Angular"]
series: ["IdeaWorlds 前端开发笔记"]
---

IdeaWorlds 前端开发笔记（五）

**本篇目标**：实现网站页头。

{{% rawhtml %}}
<table>
<tr>
<td style="width: 50px;border: 1px solid">LOGO</td>
<td style="width: 200px;border: 1px solid">导航</td>
<td style="width: 50px;border: 1px solid">操作区</td>
<td style="width: 50px;border: 1px solid">用户信息</td>
</tr>
</table>
{{% /rawhtml %}}

1. 新建模块

2. 新建组件
   ```shell
   ng g c framework/components/header --project www
   ng g c framework/components/header-logo --project www
   ng g c framework/components/header-nav --project www
   ng g c framework/components/header-operation --project www
   ng g c framework/components/header-person --project www
   ```

{{% tabs %}}
{{% tab "app.component.html" %}}
```html
<div class="page">
  <header>
    <div class="page-header">
      <!-- 这里加入 header 组件 -->
      <app-header></app-header>
    </div>
  </header>
  <div class="page-main">
    <router-outlet></router-outlet>
  </div>
  <nz-back-top></nz-back-top>
</div>
```
{{% /tab %}}
{{% /tabs %}}
