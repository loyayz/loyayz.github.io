---
title: "Hugo Github Pages 绑定域名"
date: 2022-05-28
draft: false
isCJKLanguage: true
tags: ["hugo"]
categories: ["website"]
---

## 1 域名配置
> 1. 登录[阿里云](https://aliyun.com/)，进入控制台找到域名服务
> 2. 点击`域名列表`
> 4. 点击要绑定的域名后的`解析`
> 5. 点击`添加记录`
```
记录类型：CNAME
主机记录：@
记录值：loyayz.github.io
```

## 2 网站配置
> 1. 添加解析文件

{{< filename-highlight filename="./static/CNAME" >}}
loyayz.com
{{</ filename-highlight >}}

> 2. 修改网站配置

{{< filename-highlight lang="yml" filename="./config.yml" >}}
baseURL: "https://loyayz.com/"
{{</ filename-highlight >}}

> 3. 部署后等域名解析完成，浏览器打开链接`https://loyayz.com`即可看到网站
