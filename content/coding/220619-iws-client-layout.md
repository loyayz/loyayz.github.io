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
   ng g module shared/share --flat
   ng g module framework/framework --flat
   ng g c framework/components/header
   ng g c framework/components/header-logo
   ng g c framework/components/header-nav
   ng g c framework/components/header-operation
   ng g c framework/components/header-person
   ```

2. 模块依赖

- `ShareModule`引入并重新导出其他模块常用的模块
- `FrameworkModule`引入`ShareModule`
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
import { HeaderLogoComponent } from './components/header-logo/header-logo.component';
import { HeaderNavComponent } from './components/header-nav/header-nav.component';
import { HeaderOperationComponent } from './components/header-operation/header-operation.component';
import { HeaderPersonComponent } from './components/header-person/header-person.component';


@NgModule({
  declarations: [
    HeaderComponent,
    HeaderLogoComponent,
    HeaderNavComponent,
    HeaderOperationComponent,
    HeaderPersonComponent,
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
  --iws-page-color: #f0f2f5;
  --iws-background-color: #ffffff;
  --iws-header-height: 50px;
  --iws-header-width: 1200px;
  --iws-main-width: 1000px;
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
  background: var(--iws-background-color);
  margin-bottom: 1px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  z-index: 100;
}

.page-main {
  width: var(--iws-main-width);
  min-height: calc(~"100vh - var(--iws-header-height) - var(--iws-gap)");
  margin: calc(~"var(--iws-header-height) + var(--iws-gap)") auto 0 auto;
}
```
{{% /tab %}}
{{% /tabs %}}

4. 实现网站页头

{{% tabs %}}
{{% tab "组件" %}}
```angular2html { title="app/framework/components/header/header.component.html" }
<div nz-row class="main">
  <app-header-logo nz-col nzFlex="100px"></app-header-logo>
  <app-header-nav nz-col nzFlex="auto"></app-header-nav>
  <app-header-operation nz-col nzFlex="450px"></app-header-operation>
  <app-header-person nz-col nzFlex="150px"></app-header-person>
</div>
```
{{% /tab %}}

{{% tab "样式" %}}
```less { title="app/framework/components/header/header.component.less" }
.main {
  width: 100%;
  max-width: var(--iws-header-width);
  height: var(--iws-header-height);
  line-height: var(--iws-header-height);
  margin: 0 auto;
}
```
{{% /tab %}}

{{% tab "模块" %}}
```typescript { title="app/framework/framework.module.ts" }
import { NgModule } from '@angular/core';
import { ShareModule } from '../shared/share.module';
import { HeaderComponent } from './components/header/header.component';
import { HeaderLogoComponent } from './components/header-logo/header-logo.component';
import { HeaderNavComponent } from './components/header-nav/header-nav.component';
import { HeaderOperationComponent } from './components/header-operation/header-operation.component';
import { HeaderPersonComponent } from './components/header-person/header-person.component';
// header.component.html 基于 nz-row 实现布局，因此需要引入对应的模块
import { NzGridModule } from 'ng-zorro-antd/grid';


@NgModule({
  declarations: [
    HeaderComponent,
    HeaderLogoComponent,
    HeaderNavComponent,
    HeaderOperationComponent,
    HeaderPersonComponent,
  ],
  exports: [
    HeaderComponent
  ],
  imports: [
    ShareModule,
    NzGridModule
  ]
})
export class FrameworkModule { }
```
{{% /tab %}}
{{% /tabs %}}
