---
layout: post
title: WordPress中安装插件需要FTP怎么办？
permalink: /post/wordpress-install-ftp.html
---

在初次搭建wordpress成功后,博主想安装wordpress中有趣的插件时缺发现需要FTP服务，其实不用真的搭建了一个FTP服务器

<!--more-->

这只是WordPress，留下的坑,那么,怎么绕过这道程序呢.其实很简单,我们只需在wordpress根目录找到一个

wp-config.php,添加以下代码:

```yml
/** 设置WordPress需要FTP。 */
define("FS_METHOD","direct");
define("FS_CHMOD_DIR", 0777);
define("FS_CHMOD_FILE", 0777);
```

搞定.这时又会提醒无法安装,理由是文件无法创建目录,这个好解决.给wordpress添加权限就好

```yml
chmod -R 777 wordpress的目录.
```

Enjoy
