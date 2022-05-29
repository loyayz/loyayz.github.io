---
title: "Hugo Github Pages 绑定域名"
date: 2022-05-29T00:00:00+08:00
draft: false
tags: ["hugo", "blog"]
categories: ["hugo"]
---

## 1 域名解析
> 1. 登录[阿里云](https://aliyun.com/)
> 2. 进入控制台，找到域名服务
> 3. 点击`域名列表`
> 4. 点击要绑定的域名后的`解析`
> 5. 点击`添加记录`
```
记录类型：CNAME
主机记录：@
记录值：loyayz.github.io
```

## 2 网站配置
> 1. 在根目录新建文件`static/CNAME`
```
loyayz.com
```
> 2. 修改根目录下的`config.yml`
```
baseURL: "https://loyayz.com/"
```
> 3. 部署后等域名解析完成，浏览器打开链接`https://loyayz.com`即可看到网站
