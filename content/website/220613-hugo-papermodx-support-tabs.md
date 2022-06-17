---
title: "PaperModx 模板：多标签页"
date: 2022-06-17
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
---

**需求**：新建一个模板，支持在文章正文中显示多标签页

实现步骤：

1. 创建模板

    ```html { title="./layouts/shortcodes/tabs.html" }
    <div class="x-tabs">
      <div class="x-tabs-nav"></div>
      <div class="x-tabs-content">{{ .Inner }}</div>
    </div>
    ```

    ```html { title="./layouts/shortcodes/tab.html" }
    <div class="x-tab" title="{{ .Get 0 }}">
      {{ .Inner }}
    </div>
    ```
   
2. 添加样式

    ```css { title="./assets/css/extended/custom.css" }
    .x-tabs {
      margin: 8px 0;
      display: block;
      border: 1px solid var(--tertiary);
      border-radius: 2px;
    }

    .x-tabs-nav {
      position: relative;
      display: flex;
      flex: none;
      background-color: var(--theme);
      border-bottom: 1px solid var(--tertiary);
    }

    .x-tabs-nav-item.active {
      border-bottom: 1px solid #1890ff;
    }

    .x-tabs-nav-item > span {
      display: block;
      color: var(--primary);
      font-size: 16px;
      padding: 4px 16px;
    }

    .x-tabs-nav-item.active > span,
    .x-tabs-nav-item > span:hover {
      color: #1890ff !important;
    }

    .x-tab {
      display: none;
      padding: 8px 16px;
    }

    .x-tab.active {
      display: block;
    } 
    ```
   
3. 添加脚本

    ```javascript { title="./layouts/partials/footer.html" }
    <script>
    const removeClass = (el, className) => {
        document.querySelectorAll(el).forEach($el => {
            $el.classList.remove(className);
        });
    };

    const $tabsContent = document.querySelector('.x-tabs-content');
    const $tabsNav = document.querySelector('.x-tabs-nav');

    document.querySelectorAll('.x-tab').forEach(tab => {
        const title = tab.getAttribute('title') || 'tab';
        const nav = document.createElement('div');
        nav.classList.add('x-tabs-nav-item')
        nav.innerHTML = '<span>' + title + '</span>';
        $tabsNav.append(nav);
    });

    document.querySelectorAll('.x-tabs-nav-item').forEach($navItem => {
        $navItem.addEventListener('click', e => {
            e.preventDefault();
            removeClass('.x-tabs-nav-item', 'active');
            removeClass('.x-tab', 'active');
            $navItem.classList.add('active');

            const index = Array.from($navItem.parentNode.children).indexOf($navItem);
            $tabsContent.children.item(index).classList.add('active');
        });
    });

    const $firstNav = $tabsNav.querySelector('.x-tabs-nav-item:first-child');
    if ($firstNav) {
        $firstNav.click();
    }
    </script>
    ```

4. 使用姿势

    ```html
    {{ % tabs %}}
    {{ % tab "Java" %}}
      第一个 tab，注意 {{ 和 % 之间有个空格，使用的时候记得去掉
    {{ % /tab %}}
    {{ % tab "JavaScript" %}}
      第二个 tab
    {{ % /tab %}}
    {{ % /tabs %}}
    ```

{{% tabs %}}
{{% tab "Java" %}}
```java
System.out.println("hello tabs");
```
{{% /tab %}}

{{% tab "JavaScript" %}}
```javascript
console.log("hello tabs");
```
{{% /tab %}}
{{% /tabs %}}

