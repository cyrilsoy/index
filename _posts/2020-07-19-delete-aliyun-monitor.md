---
layout: post
title: 彻底卸载阿里云服务器上的各种监控服务
permalink: /post/delete-aliyun-monitor.html
---

用过阿里云服务器（ECS、轻量应用服务器）的人应该都知道，阿里云提供的系统镜像里预装了它的各种监控服务。

<!--more-->

这些监控服务的存在使得人们可以直接在控制台看到当前系统的状态，某种程度上来说确实方便了服务器的管理。但是对于我们这种“爱折腾党”，基本没有用到这些服务的时候，而且天天被这些监控盯着还总有种被偷窥的感觉。

所以在这里介绍一下怎么卸载阿里云服务器上的各种监控服务。

## 卸载云盾（安骑士）

在[官方文档页面](https://help.aliyun.com/document_detail/31777.html)只介绍了怎么在控制台去卸载，手动卸载的操作步骤需要提交工单获取，不是很方便。

在这里介绍一种用脚本手动卸载的方法（其实用的也是官方提供的两个脚本），执行以下命令即可。

```yml
wget http://update.aegis.aliyun.com/download/uninstall.sh && chmod +x uninstall.sh &&./uninstall.sh
wget http://update.aegis.aliyun.com/download/quartz_uninstall.sh && chmod +x quartz_uninstall.sh && ./quartz_uninstall.sh
```

测试发现上面的脚本运行完之后可能还会有一些服务/文件/文件夹残留，所以我们手动清理一下，顺便把刚刚下下来的两个脚本文件也删了（如果提示文件不存在就不用管了）。

```yml
sudo rm -r /usr/local/aegis
sudo systemctl disable aliyun.service
sudo rm /usr/sbin/aliyun-service
sudo rm /usr/sbin/aliyun-service.backup
sudo rm /usr/sbin/aliyun_installer
sudo rm /etc/systemd/system/aliyun.service
sudo rm /lib/systemd/system/aliyun.service
rm uninstall.sh quartz_uninstall.sh
```

## 卸载云监控（Cloudmonitor）

云监控有 Java 版本和 Go 语言版本两种（Java 版本已经不再提供升级了，现在新开的服务器应该都是 Go 语言版本的），可以根据其安装目录 `/usr/local/cloudmonitor/` 下的文件名判断你的服务器上装的具体是哪一种。下面分别介绍两个版本的卸载方法。

卸载云监控 Go 语言版
云监控 Go 语言版的可执行文件名为 `CmsGoAgent.linux-${ARCH}` ，其中的 ARCH 根据 Linux 架构的不同，分为 `amd64` 和 `386` ，可以在其安装目录 `/usr/local/cloudmonitor/` 下找到具体的文件名。

为了方便表述，下面的命令中也使用 `${ARCH}` 替代文件名中的系统架构部分。要能够直接复制下面的命令行去执行的话，可以先设置一下临时环境变量（相当于后面输入命令中的 `${ARCH}` 会被自动替换成我们在这里设置的值）

```yml
# 64 位系统
export ARCH=amd64

# 32 位系统
export ARCH=386
```

或者的话你也可以在找到具体的文件名后自行替换。下面是卸载相关的一些命令：

```yml
# 从系统服务中移除
/usr/local/cloudmonitor/CmsGoAgent.linux-${ARCH} uninstall

# 停止
/usr/local/cloudmonitor/CmsGoAgent.linux-${ARCH} stop

# 卸载
/usr/local/cloudmonitor/CmsGoAgent.linux-${ARCH} stop && \
/usr/local/cloudmonitor/CmsGoAgent.linux-${ARCH} uninstall && \
rm -rf /usr/local/cloudmonitor
```

附上官方文档页面链接：https://help.aliyun.com/document_detail/97929.html

## 卸载云监控 Java 版

```yml
# 停止
/usr/local/cloudmonitor/wrapper/bin/cloudmonitor.sh stop

# 卸载
/usr/local/cloudmonitor/wrapper/bin/cloudmonitor.sh remove && \
rm -rf /usr/local/cloudmonitor
```

附上官方文档页面链接：https://help.aliyun.com/knowledge_detail/38859.html

## 转载说明

[彻底卸载阿里云服务器上的各种监控服务](https://xirikm.net/2019/331-1.html)

Enjoy
