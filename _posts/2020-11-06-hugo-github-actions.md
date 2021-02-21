---
layout: post
title: Hugo + Github Actions 实现自动化部署
permalink: /post/hugo-github-actions.html
---

不折腾点东西总觉得浑浑噩噩，折腾点东西搞得脑袋晕晕乎乎！

<!--more-->

## 上传 Hugo 源文件

（注：如已把 Hugo 程序 push 到 GitHub 上，直接跳过看下一步。）

点击下载：[https://github.com/lmm214/edui/archive/master.zip](https://github.com/lmm214/edui/archive/master.zip)

GitHub 上新建一个 repo，并只需保留以下文件上传到 `main` ，同时随手建个 `gh-pages` 分支：

```yml
├── archetypes
│   └── default.md
├── config.toml  //Hugo 程序配置文件
├── content
│   ├── about.md
├── data
│   └── links.toml //导航数据，一个文件搞定
└── themes
    └── webstack  //Hugo 主题
        ├── layouts
        │   └── home.html  //主页
        └── theme.toml  //主题配置文件
```
		
## 添加 Github Actions 代码

实现效果，直接网页上修改 data/links.toml 或任意文件，触发 Actions 自动化运行 Hugo 程序生成静态文件并推送到 gh-pages 分支上，等待几十秒可看到更新 https://lmm214.github.io/edui/

具体操作：

![Github-Actions](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/hugo-github-actions/tokens-1.png)

![Github-Actions](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/hugo-github-actions/tokens-2.png)

 - 点 https://github.com/settings/tokens 新建一个，勾选 repo 和 workflow ,暂存；

![Github-Actions](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/hugo-github-actions/secrets.jpeg)

 - 进项目 settings/secrets 新建标题为 personal_token ，内容是刚创建的 tokens ;

 - 回项目，点 Actions -- New wordflow -- Set up a workflow yourself ，添加如下代码：

```yml
name: Deploy Hugo # 任君喜欢

on:
  push:
    branches:
      - main # main 更新触发

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
      - uses: actions/checkout@v1

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: latest

      - name: Build 
        run: hugo

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.personal_token }} # personal_token 这里新建一个 https://github.com/settings/tokens
          PUBLISH_BRANCH: gh-pages # 推送到当前 gh-pages 分支
          commit_message: ${{ github.event.head_commit.message }}
```

👌 等，3、2、1，看看 `Actions` 顺利不，再看看 `gh-pages` 静态文件更新了不，最终打开 Pages ，祝贺！

日志？主题？统统 GitHub 网页端搞定！当然，调试主题什么的还是建议本地进行。

## 后话一点点

Hugo 生成静态文件甩 Hexo 几光年，部署安装无依赖，试用门槛较低。看着官方文档，程序、主题自定义也比较强悍。

搜遍全网教程，要不就是太详细，一长串代码加注释，供大于求，要不略过。幸好，也算折腾成功！ ✌️

## 致谢：

 - 官方 Hugo Templates：[https://gohugo.io/templates/](https://gohugo.io/templates/)
 - 中文系列教程翻译：[https://www.rectcircle.cn/series/hugo/](https://www.rectcircle.cn/series/hugo/)
 - Hugo 中文帮助文档：[https://hugo.aiaide.com/](https://hugo.aiaide.com/)
 - Hugo + Github Actions 实现自动化部署：[https://immmmm.com/hugo-github-actions/](https://immmmm.com/hugo-github-actions/)

Enjoy
