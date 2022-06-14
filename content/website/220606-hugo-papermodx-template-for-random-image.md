---
title: "PaperModx 模板：随机显示图片"
date: 2022-06-06
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
---

**需求**：新增模板用来随机显示图片，支持`partials`和`shortcodes`

👇 **随机的图片，刷新页面看看效果** 👇

{{< random_image >}}

## 1 实现原理

基于`unsplash`提供的随机图片链接生成`<img>`
```html
<img loading="lazy" alt="" class="x-random-img" 
     src="https://source.unsplash.com/random/720x400?star" width="720" height="400"
     data-topic="star"/>
```
- `https://source.unsplash.com/random`：会被重定向到一张随机图片
- `/720x400`：限定图片宽高，unsplash 会缩放或截取原图
- `?star`：限定图片主题，可为空，多个主题用逗号隔开`wallpapers,star,nature`
- `width="720" height="400"`：限定`<img>`宽高，也可用 css 样式控制
- `data-topic="star"`：我设的属性，用来生成链接

## 2 实现过程

### 2.1 创建 js
```js { title="./assets/js/random-image.js" }
import * as params from '@params';

let opt = { api: 'https://source.unsplash.com/random/${w}x${h}?${t}', width: 1, height: 0.5, topic: '' };
Object.assign(opt, params.opt || {});

function _cssVar(key, defaultVal) {
    let num = window.getComputedStyle(document.body).getPropertyValue(key);
    if (!num) {
        return defaultVal;
    }
    num = num.replace('px', '');
    return Number.parseInt(num);
}

let defaultWidth = Number.parseFloat(opt.width);
if (defaultWidth <= 1) {
    defaultWidth = _cssVar('--main-width', 720) * defaultWidth;
}

let defaultHeight = Number.parseFloat(opt.height);
if (defaultHeight <= 1) {
    defaultHeight = window.innerHeight * defaultHeight;
}

document.querySelectorAll('.x-random-img').forEach($img => {
    let _src = $img.getAttribute('src') || '#';
    if (_src !== '#') {
        return;
    }
    let _topic = $img.getAttribute('data-topic') || opt.topic;
    let _width = Number.parseInt($img.getAttribute('width') || defaultWidth);
    let _height = Number.parseInt($img.getAttribute('height') || defaultHeight);
    _width = (_width % 10 === 0 ? _width : _width + 10 - _width % 10) + '';
    _height = (_height % 10 === 0 ? _height : _height + 10 - _height % 10) + '';

    _src = opt.api
        .replace('${w}', _width)
        .replace('${h}', _height)
        .replace('${t}', _topic);
    $img.setAttribute('src', _src);
    $img.setAttribute('width', _width);
    $img.setAttribute('height', _height);
});
```
### 2.2 引用 js
```html { title="./layouts/partials/footer.html" }
<!-- 在最后面加入这段 -->
{{- if (site.Params.randomImage.enable | default true) }}
{{- $random_image_js := resources.Get "js/random-image.js" | js.Build (dict "params" (dict "opt" .Site.Params.randomImage)) | resources.Minify }}
<script defer crossorigin="anonymous" src="{{ $random_image_js.RelPermalink }}"></script>
{{- end -}}
```

### 2.3 添加样式
```css { title="./assets/css/extended/custom.css" }
.x-random-img {
  border-radius: 0 !important;
}

/* 文章页面内的随机图片水平居中 */
.post-content .x-random-img {
  margin: 1rem auto;
}
```

### 2.4 新增模板
```html { title="./layouts/partials/templates/random_image.html" }
<img loading="lazy" alt="" class="x-random-img {{ .class }}"
     {{ with .width }}width="{{.}}"{{ end }}
     {{ with .height }}height="{{.}}"{{ end }}
     {{ with .topic }}data-topic="{{.}}"{{ end }}
/>
```
```html { title="./layouts/shortcodes/random_image.html" }
<img loading="lazy" alt="" class="x-random-img {{ .Get "class" }}"
     {{ with .Get "width" }}width="{{.}}"{{ end }}
     {{ with .Get "height" }}height="{{.}}"{{ end }}
     {{ with .Get "topic" }}data-topic="{{.}}"{{ end }}
/>
```

## 3. 使用姿势

- shortcodes

```html
<!-- 因 Hugo 会自动解析模板，这里 {{ 和 < 之间有个空格，使用时记得删除 -->
<!-- 最简方式 -->
{{ < random_image >}}
<!-- 完整参数 -->
{{ < random_image width="" height="" topic="" class="" >}}
```
- partials
```html
<!-- 最简方式 -->
{{- partial "templates/random_image.html" }}
<!-- 完整参数 -->
{{- partial "templates/random_image.html" (dict "width" "" "height" "" "topic" "" "class" "") }}
```

- 参数说明
  - 模板参数
| 参数    | 说明     | 默认值      |
|--------|---------|----------|
| width  | 图片宽   | `config.yml`的`params.randomImage.width`   |
| height | 图片高   | `config.yml`的`params.randomImage.height`  |
| topic  | 图片主题 |     |
| class  |`<img>`样式 |  |
  - 网站配置
```yml { title="./config.yml" }
params:
    # 全局默认值，非必须
    randomImage:
        # 是否启用，默认 true
        enable: true
        # 图片链接，默认值如下，可替换为其他服务提供者
        api: 'https://source.unsplash.com/random/${w}x${h}?${t}'
        # 图片宽，默认 1，不大于 1 时将自动计算为 页面宽*width 并向上取整
        # 解析时将替换 randomImage.api 中 ${w}
        width: ""
        # 图片高，默认 0.5，不大于 1 时将自动计算为 屏幕高*height 并向上取整
        # 解析时将替换 randomImage.api 中 ${h}
        height: ""
        # 图片主题，无默认值
        # 解析时将替换 randomImage.api 中 ${t}
        topic: ""
```
