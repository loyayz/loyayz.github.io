---
title: "允许禁用校验文件完整性"
date: 2022-06-14
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
---

**需求**：加一个参数控制是否禁用`head.html`中的`integrity`校验。

**起因**：我有个网站的部署方式是手动打包后`git push`到远端，服务器`git pull`打包后的文件。部署之后网站样式都不见了，打开调试工具F12发现报错。

```text
Failed to find a valid digest in the 'integrity' attribute for resource ...... The resource has been blocked.
```

搜索后发现是因为 PaperMod 的模板 `head.html` 中有几个资源的引用加了`integrity`属性防止被篡改，[属性说明查看这里 | mozilla mdn](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#attr-integrity)

```html { title="./layouts/partials/head.html" linenostart=69 }
<link crossorigin="anonymous" href="{{ $stylesheet.RelPermalink }}" integrity="{{ $stylesheet.Data.Integrity }}" rel="preload stylesheet" as="style">
```
```html { title="./layouts/partials/head.html" linenostart=82 }
<script defer crossorigin="anonymous" src="{{ $search.RelPermalink }}" integrity="{{ $search.Data.Integrity }}"></script>
```
```html { title="./layouts/partials/head.html" linenostart=94 }
<script defer crossorigin="anonymous" src="{{ $highlight.RelPermalink }}" integrity="{{ $highlight.Data.Integrity }}"
```

猜测可能是 git 提交时某个环节把打包文件中的空格或者换行符去掉了导致校验失败，所以加了个全局参数控制是否需要这个属性，如果你也有这个报错可以试试。

实现方式：

```html
<!-- 上面三个 integrity 用 if 包起来，像这样 -->
{{- if (not site.Params.disableResourceIntegrity) }} integrity="..." {{- end }}
```

使用姿势：
```yml { title="./config.yml" }
params:
    disableResourceIntegrity: true
```




