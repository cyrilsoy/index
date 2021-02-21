---
layout: post
title: Hexo + Github Pages建立一個個人獨立博客
permalink: /post/github-hexo.html
---

`Hexo` 是一個快速,簡潔,高效的博客框架.

<!--more-->

`Hexo` 使用 `Markdown` 解析文章,並在幾秒鐘內就可以生成帶有精美主題的靜態網頁.

## 準備環境

執行更新包

```yml
apt-get update
```

安裝`Git`

```yml
apt install git-core -y
```

檢查 `Git` 版本

```yml
git --version
```

安裝 `Node.js`

使用以下命令安裝 `Node.js`

使用一鍵式命令或程序包管理器安裝 `Node.Js`

以下分別介紹. 這是安裝 `Node.Js` 的程序包管理器

官方資料: https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions-enterprise-linux-fedora-and-snap-packages

`Github`源: https://github.com/nodesource/distributions

執行以下命令進行安裝 `curl`

```yml
apt install curl
```

安裝 `Node.js v11.x` 版本

```yml
curl -sL https://deb.nodesource.com/setup_11.x | bash -
apt-get install -y nodejs
```

查看 `Node.js` 版本

```yml
node --version
```

查看 `NPM` 版本

```yml
npm --version
```

## 安裝 `Hexo`

使用命令創建 `Hexo` 文件夾

```yml
mkdir hexo
cd hexo
```

使用命令安裝 `Hexo`

```yml
npm install hexo-cli -g
```

查看 `Hexo` 版本號

```yml
hexo -v
```

初始化 `Hexo`

```yml
hexo init
```

測試安裝成功

打開 `Hexo` 服務

```yml
hexo server
```

打開瀏覽器訪問 `IP:4000` 查看成功的博客頁面

## 將您的博客部署到 `GitHub`

如果您沒有要先註冊的 `GitHub` 帳戶,則直接轉到 `GitHub` 帳戶即可.

設置 `user.name` 和 `user.email`

將以下命令中的“您的用戶名”和“您的用戶名”替換為您自己的

```yml
git config --global user.name "Your user.name"
git config --global user.email "You user.email"
```

生成 `ssh` 密鑰

`User.email` 是您自己註冊 `GitHub` 的郵箱

```yml
ssh-keygen -t rsa -C user.email
```

輸入保存到下面的路徑

```yml
/root/.ssh/id_rsa
```

然後只需按`Enter`.

查看 `SSH` 密鑰

```yml
nano /.ssh/id_rsa.pub
```

在 `GitHub` 帳戶下添加SSH密鑰

創建一個 `GitHub` 存儲庫

命名格式為“帳戶的用戶名” .github.io

例如：`userName.github.io` 創建 `GitHub` 存儲庫

如果不是以這種格式創建資源庫,則在創建域綁定後,需要轉到 `Github` 項目中的“設置”,選擇 `GitHub Pages` 在 `Source` 中選擇 `master` 分支,然後單擊 `Save` .

修改 `Hexo` 配置文件
 
打開 `Hexo` 文件

```yml
_config.yml
```

修改相應的部分

```yml
deploy:
  type: git
  repo: git@github.com:(BoView)/(BoView).github.io.git #Replace the parentheses with your own username and warehouse name, and remove the parentheses.
  branch: master
```

保存

測試和部署

清除靜態頁面

```yml
hexo clean
```

生成靜態頁面

```yml
hexo g
```

將公共文件內容部署到 `Github` 存儲庫

```yml
hexo d
```

如果部署遇到錯誤,請首先運行以下命令

```yml
npm install hexo-deployer-git --save
```

然後重新部署

```yml
hexo d
```

此時,您可以打開瀏覽器訪問 `userName.github.io` （GitHub存儲庫名稱）以訪問構建的博客頁面.

## 綁定域名

添加解析記錄

如果要繼續瀏覽域名,前提是要擁有自己的域名. 如果可以通過上述倉庫名稱訪問它,則可以跳過此步驟.

轉到您自己的域名,然後將解析記錄類型添加到 `CNAME` . 主機記錄是 `@CNAME` 選擇默認值, `TTL` 選擇是 `600` ,記錄值是 `github` 的存儲庫名稱 `userName.github.io`

配置 `Hexo` 文件

創建一個 `CNAME` 配置文件

```yml
touch ~/hexo/source/CNAME
```

在 `CNAME` 文件下添加剛解析的域名.

然後重新部署

```yml
hexo g
hexo d
```

## `Hexo` 博客已建立

恭喜啦

此時,打開瀏覽器訪問自己的域名,就可以訪問自己的博客,打開您自己的博客之旅.

Enjoy
