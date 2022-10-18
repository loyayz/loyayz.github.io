---
title: "IdeaWorlds 网站布局"
date: 2022-06-19
draft: false
isCJKLanguage: true
tags: ["IdeaWorlds", "Angular"]
series: ["IdeaWorlds 前端开发笔记"]
---

IdeaWorlds 前端开发笔记（四）

**本篇目标**：实现网站布局，其中页头全站固定，内容区将随着导航切换而显示对应的组件。

{{% rawhtml %}}
<table>
<tr>
<td style="width: 50px;border: 1px solid">LOGO</td>
<td style="width: 200px;border: 1px solid">导航</td>
<td style="width: 100px;border: 1px solid">操作区</td>
<td style="width: 50px;border: 1px solid">用户信息</td>
</tr>
<tr><td colspan="4" style="height: 150px;border: 1px solid">content</td></tr>
</table>
{{% /rawhtml %}}

1. 新建模块和组件
   ```shell
   ng g module shared/share --flat --project www
   ng g module framework/framework --flat --project www
   ng g c framework/components/header --project www
   ng g c framework/components/header-sidebar --project www
   ng g c framework/components/header-nav --project www
   ng g c framework/components/header-operation --project www
   ng g c framework/components/header-person --project www
   ```

2. 模块依赖

- `ShareModule`引入并重新导出其他模块常用的模块
- `FrameworkModule`引入`ShareModule`并导出`HeaderComponent`
- `AppModule`引入`FrameworkModule`

{{% tabs %}}
{{% tab "AppModule" %}}
```typescript { title="app/app.module.ts" }
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

import { NZ_I18N,zh_CN } from 'ng-zorro-antd/i18n';
import { registerLocaleData } from '@angular/common';
import zh from '@angular/common/locales/zh';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { FrameworkModule } from './framework/framework.module';

registerLocaleData(zh);

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    AppRoutingModule,
    FrameworkModule
  ],
  providers: [{ provide: NZ_I18N, useValue: zh_CN }],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
{{% /tab %}}

{{% tab "FrameworkModule" %}}
```typescript { title="app/framework/framework.module.ts" }
import { NgModule } from '@angular/core';
import { ShareModule } from '../shared/share.module';
import { HeaderComponent } from './components/header/header.component';
import { HeaderSidebarComponent } from './components/header-sidebar/header-sidebar.component';
import { HeaderNavComponent } from './components/header-nav/header-nav.component';
import { HeaderOperationComponent } from './components/header-operation/header-operation.component';
import { HeaderPersonComponent } from './components/header-person/header-person.component';


@NgModule({
  declarations: [
    HeaderComponent,
    HeaderNavComponent,
    HeaderOperationComponent,
    HeaderPersonComponent,
    HeaderSidebarComponent,
  ],
  exports: [
    HeaderComponent
  ],
  imports: [
    ShareModule
  ]
})
export class FrameworkModule { }
```
{{% /tab %}}

{{% tab "ShareModule" %}}
```typescript { title="app/shared/share.module.ts" }
import { NgModule } from '@angular/core';
import { RouterModule } from '@angular/router';
import { CommonModule } from '@angular/common';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
import { HttpClientModule } from '@angular/common/http';


@NgModule({
  declarations: [
  ],
  imports: [CommonModule, FormsModule, ReactiveFormsModule, HttpClientModule, RouterModule],
  exports: [CommonModule, FormsModule, ReactiveFormsModule, HttpClientModule, RouterModule]
})
export class ShareModule { }
```
{{% /tab %}}
{{% /tabs %}}

3. 实现网站布局

{{% tabs %}}
{{% tab "组件" %}}
```angular2html { title="app/app.component.html" }
<div class="page">
  <header class="page-header">
    <app-header></app-header>
  </header>
  <div class="page-main">
    <router-outlet></router-outlet>
  </div>
</div>
```
{{% /tab %}}

{{% tab "样式" %}}
```less { title="styles.less" }
:root {
  --iws-gap: 16px;
  --iws-header-height: 50px;
  --iws-header-max-width: 1200px;
  --iws-main-max-width: 1200px;
}

@media (max-width: 576px) {
  :root {
    --iws-gap: 12px;
    --iws-header-height: 40px;
  }
}
```
```less { title="app/app.component.less" }
.page {
  box-sizing: border-box;
  display: flex;
  flex: auto;
  flex-direction: column;
  background: var(--iws-page-color);
}

.page-header {
  position: fixed;
  width: 100%;
  height: var(--iws-header-height);
  margin-bottom: 1px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  z-index: 100;
}

.page-main {
  width: 100%;
  max-width: var(--iws-main-max-width);
  min-height: calc(~"100vh - var(--iws-header-height) - var(--iws-gap)");
  margin: calc(~"var(--iws-header-height) + var(--iws-gap)") auto 0 auto;
}
```
{{% /tab %}}
{{% /tabs %}}

切换导航时路由对应的组件将展示在 `<router-outlet></router-outlet>`

4. 实现网站页头

{{% tabs %}}
{{% tab "组件" %}}
```angular2html { title="app/framework/components/header/header.component.html" }
<div class="main">
  <app-header-sidebar class="header-sidebar"></app-header-sidebar>
  <app-header-nav class="header-nav"></app-header-nav>
  <app-header-operation class="header-operation" ></app-header-operation>
  <app-header-person class="header-person"></app-header-person>
</div>
```
{{% /tab %}}

{{% tab "样式" %}}
```less { title="app/framework/components/header/header.component.less" }
.main {
  display: flex;
  width: 100%;
  max-width: var(--iws-header-max-width);
  height: var(--iws-header-height);
  line-height: var(--iws-header-height);
  margin: 0 auto;
}
```
{{% /tab %}}
{{% /tabs %}}
