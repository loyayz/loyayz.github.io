---
title: "PaperMod 文章页展示所属系列的文章列表"
date: 2022-06-12
draft: false
isCJKLanguage: true
tags: ["hugo", "paper-modx"]
series: ["PaperModx 定制文章页"]
---

**需求**：在文章页面底部展示该文章所属系列的文章列表

实现步骤：

1. 新建模板
```html { title="./layouts/partial/series-posts.html" }
{{- if .Params.series }}
<div class="x-post-series">
{{- $series := .Site.Taxonomies.series}}
{{- range .Params.series}}
{{- $seriesName := . }}
    
{{- range $name, $value := $series }}
    {{- if or (eq $name $seriesName) (eq ($name|urlize) ($seriesName|urlize) ) }}
    <details open>
        <summary>{{ $seriesName }}</summary>
        <ol>
            {{- range $value.Pages.ByDate }}
            <li><a href="{{.Permalink}}">{{.LinkTitle}}</a>
                <sub>{{- .Date.Format .Site.Params.DateFormat -}}</sub>
            </li>
            {{- end }}
        </ol>
    </details>
    {{- end }}
{{- end }} {{/* end range site series */}}
    
{{- end }} {{/* end range page series */}}
</div>
{{- end }} {{/* end .Params.series */}}
```

2. 添加样式
```css { title="./assets/css/extended/custom.css" }
.x-post-series {
  padding: 16px 0;
}
```

3. 编辑文章模板
```html { title="./layouts/_default/single.html" linenostart=38 }
  <footer class="post-footer">
    <!--  添加下面这几行  -->
    {{- if and site.Params.ShowSeriesInPost (ne .Params.showSeries false ) }}
    {{- partial "series-posts.html" . -}}
    {{- end }}
```

4. 使用姿势
```yml { title="./config.yml" }
params:
    # 是否在文章页显示所属系列的文章列表
    ShowSeriesInPost: true
```

设置文章所属系列，比如本篇是这样：
```yml
---
title: "PaperMod 文章页展示所属系列的文章列表"
date: 2022-06-12
draft: false
tags: ["hugo", "paper-modx"]
series: ["PaperModx 定制文章页"]
# 是否在本页展示系列文章，默认值 true
showSeries: true
---
```

