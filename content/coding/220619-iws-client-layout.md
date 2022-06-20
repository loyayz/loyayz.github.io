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
<table style="width: 300px;border: 1px solid">
<tr><td style="width: 300px">header</td></tr>
<tr><td style="height: 100px">content</td></tr>
</table>
{{% /rawhtml %}}

1. 编辑应用组件

   ```angular2html { title="src/app/app.component.html" }
   <div class="page">
     <header>
       <div class="page-header"></div>
     </header>
     <div class="page-main">
       <router-outlet></router-outlet>
     </div>
   </div>
   ```
   
2. 添加样式

   ```less { title="src/styles.less" }
   :root {
     --iws-gap: 16px;
     --iws-page-color: #f0f2f5;
     --iws-background-color: #ffffff;
     --iws-header-height: 50px;
     --iws-header-width: 1200px;
     --iws-main-width: 1000px;
   }
   ```

   ```less { title="src/app/app.component.less" }
   .page {
     box-sizing: border-box;
     display: flex;
     flex: auto;
     flex-direction: column;
     background: var(--iws-page-color);
   }

   header {
     position: fixed;
     width: 100%;
     height: var(--iws-header-height);
     background: var(--iws-background-color);
     margin-bottom: 1px;
     box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
     z-index: 100;
   }

   .page-header {
     width: var(--iws-header-width);
     line-height: var(--iws-header-height);
     margin: 0 auto;
   }

   .page-main {
     width: var(--iws-main-width);
     min-height: calc(~"100vh - var(--iws-header-height) - var(--iws-gap)");
     margin: calc(~"var(--iws-header-height) + var(--iws-gap)") auto 0 auto;
   }
   ```
