
---
title: "IdeaWorlds 前端自动化部署"
date: 2022-06-18
draft: false
isCJKLanguage: true
tags: ["IdeaWorlds", "Angular"]
series: ["IdeaWorlds 前端开发笔记"]
---

IdeaWorlds 前端开发笔记（三）

**本篇目标**：介绍本项目的前端自动化部署。

趁 618 买了台腾讯云的服务器折腾了下，因此先提前写下前端部署的笔记，完整流程：
> 1. 开通[容器镜像服务](https://console.cloud.tencent.com/tcr) -> 创建命名空间 ideaworlds -> 创建两个镜像仓库 www、console 
> 2. 服务器安装 Docker -> [登录私服](https://cloud.tencent.com/document/product/1141/63910)
> 3. 服务器安装 Nginx 并反向代理前端目录
> 4. GitHub Action 监听部署分支

## 1. 前端工程

### 1.1 GitHub Action 流程配置

- 自动部署流程
  - 从分支名解析出当前要部署的项目（例：`release/www` -> 部署`www`）
  - 设置 Docker 镜像名：{私有仓库}/{命名空间}/{项目名}
  - 获取当前日期、checkout 最新代码、设置 nodejs 环境
  - npm 打包要部署的项目，并将打包后的文件移到`./deploy/dist/`
  - Docker 打包`./deploy/`：{镜像名}:latest、{镜像名}:当前日期
  - Docker 登录私有仓库，推送刚打包的两个镜像到仓库
  - ssh 登录服务器（注意：该服务器需先手动登录 Docker 私有仓库）
    - 创建前端目录：/app/{命名空间}/{项目名}
    - 拉取刚推送的镜像：镜像名:latest
    - 映射前端目录启动一个临时容器，执行成功后将在前端目录创建三个子目录
      - prev    （存放上次的文件，即先从 latest/ 移到 prev/ ）
      - latest  （存放最新的文件）
      - latest_bak （存放最新的文件）
    - 删除镜像
  ```yml { title="./.github/workflows/deploy.yml" }
  name: deploy

  on:
    push:
      branches: [ "release/*" ]
    workflow_dispatch:
  
  env:
    docker-registry: ccr.ccs.tencentyun.com
    docker-namespace: ideaworlds
    remote-workdir: /app
  
  jobs:
    deploy:
      runs-on: ubuntu-latest
      steps:
        - name: Set env.project
          run: |
            echo "project=${GITHUB_REF#refs/heads/release/}" >> $GITHUB_ENV
  
        - name: Set env.image
          run: |
            echo "image=${{ env.docker-registry }}/${{ env.docker-namespace }}/${{ env.project }}" >> $GITHUB_ENV
  
        - name: Get Time
          id: time
          uses: nanzm/get-time-action@v1.1
          with:
            timeZone: 8
            format: 'YYMMDD'
  
        - uses: actions/checkout@v3
        - uses: actions/setup-node@v3
          with:
            node-version: 18
            cache: 'npm'
  
        - name: npm build
          run: |
            npm ci
            npm run build:${{ env.project }}
            mv ./dist/${{ env.project }}/ ./deploy/dist/
  
        - name: docker build
          run: |
            cd ./deploy
            docker build -t ${{ env.image }}:latest .
            docker tag ${{ env.image }}:latest ${{ env.image }}:${{ steps.time.outputs.time }}
  
        - name: Login to Docker Registry
          uses: docker/login-action@v2
          with:
            registry: ${{ env.docker-registry }}
            username: ${{ secrets.DOCKER_USER }}
            password: ${{ secrets.DOCKER_TOKEN }}
  
        - name: Push to Docker Registry
          run: |
            docker push ${{ env.image }}:latest
            docker push ${{ env.image }}:${{ steps.time.outputs.time }}
  
        - name: Run Docker Image
          uses: appleboy/ssh-action@master
          with:
            host: ${{ secrets.REMOTE_HOST }}
            username: ${{ secrets.REMOTE_USER }}
            key: ${{ secrets.REMOTE_KEY }}
            script_stop: true
            script: |
              mkdir -p ${{ env.remote-workdir }}/${{ env.docker-namespace }}/${{ env.project }}
              docker pull ${{ env.image }}:latest
              docker run --rm -v ${{ env.remote-workdir }}/${{ env.docker-namespace }}/${{ env.project }}:/app/data ${{ env.image }}:latest
              docker rmi ${{ env.image }}:latest
  ```

### 1.2 Angular 打包
- 开启 gzip

  ```shell
  npm install --save-dev gulp-gzip
  npm install --save-dev gulp
  ```
  
  ```javascript { title="./gulpfile.js" }
  // gulpfile.js 与 package.json 同级
  let gulp = require('gulp');
  let gzip = require('gulp-gzip');

  gulp.task('compress', function () {
    return gulp.src(['./dist/**/*.js', './dist/**/*.css'])
      .pipe(gzip())
      .pipe(gulp.dest('./dist'));
  });
  ```

- 修改部署包大小限制

  Angular 默认打包大小超过 1m 会失败，我们的原部署包刚好超过 1m 一点点，gzip 压缩后不到 200k，因此需要放开这个限制。

  ```json { title="./angular.json" }
  {
    "projects": {
      "www": {
        "architect": {
          "build": {
            "configurations": {
              "production": {
                {
                  "type": "initial",
                  "maximumWarning": "1mb",
                  "maximumError": "2mb"    修改这个
                }
      },
      "console": 同上面 
  }
  ```

- 编写打包脚本

  ```json { title="./package.json" }
  {
    "scripts": {
      "ng": "ng",
      "start:www": "ng serve --project www --open",
      "build:www": "ng build --project www && gulp compress",
      "start:console": "ng serve --project console --open",
      "build:console": "ng build --project console && gulp compress",
      "build": "npm run build:www && npm run build:console"
    }
  }
  ```

### 1.3 Docker 打包

- Dockerfile
  ```dockerfile { title="./deploy/Dockerfile" }
  FROM alpine:latest
  COPY . /app
  ENTRYPOINT ["sh", "/app/docker-entrypoint.sh"]
  ```
- Docker 容器内自动执行脚本
  ```shell { title="./deploy/docker-entrypoint.sh" }
  #!/bin/sh

  mkdir -p ./app/data/prev/
  mkdir -p ./app/data/latest/
  mkdir -p ./app/data/latest_bak/
  rm -rf ./app/data/prev/
  mv ./app/data/latest/ ./app/data/prev/
  cp -r ./app/dist/ ./app/data/latest/
  rm -rf ./app/data/latest_bak/
  cp -r ./app/dist/ ./app/data/latest_bak/
  ```

## 2. 服务器

### 2.1 安装 Docker

- 安装文档：[Docker 官网](https://docs.docker.com/engine/install/)、[腾讯云](https://cloud.tencent.com/document/product/1207/45596)
- [开通容器镜像服务并登录私服](https://cloud.tencent.com/document/product/1141/63910)

### 2.2 配置 Nginx

使用`docker compose`启动 Nginx 服务

- 创建 Nginx 目录
  ```shell
  mkdir -p /data/nginx 
  mkdir -p /data/nginx/conf.d 
  ```
- 编写 Docker Compose 配置
  ```yml { title="/data/nginx/docker-compose.yml" }
  version: '3.0'

  services:
    nginx:
      restart: always
      image: nginx:latest
      container_name: nginx
      ports:
        - 80:80
        - 443:443
      volumes:
        - ./nginx.conf:/etc/nginx/nginx.conf
        - ./conf.d:/etc/nginx/conf.d
        - ./logs:/var/log/nginx
  ```
  > 使用`nginx:latest`镜像启动一个名为`nginx`的容器，并映射服务器的 80 和 443 端口。
- 启动 Nginx 服务
  ```shell
  docker compose up -d
  ```
- 编写两个脚本用于更新 Nginx 服务
  ```shell { title="/data/nginx/test_conf.sh" }
  docker exec nginx nginx -t
  ```
  ```shell { title="/data/nginx/reload_nginx.sh" }
  docker exec nginx nginx -s reload
  ```
  > 修改 Nginx 配置后
  > 
  > 执行 `sh ./test_conf.sh` 校验配置是否正常
  > 
  > 执行 `sh ./reload_nginx.sh` 重启 Nginx 服务
- 配置前端反向代理
  - 在`./conf.d/`目录下新建 Nginx 配置文件并指向前端目录`/app/ideaworlds/`
    - [www.ideaworlds.info](https://ideaworlds.info) -> `www/latest`、`www/latest_bak`
    - [console.ideaworlds.info](https://console.ideaworlds.info) -> `console/latest`、`console/latest_bak`
  - 重启 Nginx 服务

### 2.3 生成密钥

[腾讯云文档](https://cloud.tencent.com/document/product/1207/44573)

## 3. GitHub

进入 [远程仓库的设置选项](https://github.com/idea-worlds/idea-worlds/settings) 在`Secrets/actions`添加几个仓库的密钥

| key          | 说明            |
|--------------|---------------|
| DOCKER_USER  | Docker 私服用户名  |
| DOCKER_TOKEN | Docker 私服用户密码 |
| REMOTE_HOST  | 远程服务器 ip      |
| REMOTE_USER  | 远程服务器 ssh 用户名 |
| REMOTE_KEY   | 远程服务器 ssh 私钥  |


## 4. 总结

以上全部为一次性配置后续无需再修改，前端工程中推送代码到 release/ 开头的分支将触发 GitHub Action 部署流程，自动打包前端代码更新到服务器对应的目录下。
