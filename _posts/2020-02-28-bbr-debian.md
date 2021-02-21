---
layout: post
title: Debian 安裝 Google BBR
permalink: /post/bbr-debian.html
---

`Google BBR` 是 `Google` 提出的一種新的擁塞控制算法,可使 `Linux` 服務器顯著提高吞吐量並減少 `TCP` 連接延遲.

<!--more-->

## 開源地址

https://github.com/google/bbr

## 系统版本

 - Debian8

## 内核版本

 - 4.9.0-rc8
 
## 安装教程

### 下载最新内核 

```yml
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.9/linux-image-4.9.0-040900-generic_4.9.0-040900.201612111631_amd64.deb
```

 - 最新内核[查看](http://kernel.ubuntu.com/~kernel-ppa/mainline) 

### 安装内核

```yml
dpkg -i linux-image-4.9.0*.deb
```

### 删除其余内核

```yml
dpkg -l|grep linux-image 
apt-get purge
```

### 更新 grub 系统引导文件并重启

```yml
update-grub
reboot
```

## 开启 bbr

```yml
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```

### 保存生效

```yml
sysctl -p
```

执行 `sysctl net.ipv4.tcp_available_congestion_control`

如果结果中有 `bbr`, 则证明你的内核已开启 `bbr`

执行 `lsmod | grep bbr`, 看到有 `tcp_bbr` 模块即说明 `bbr` 已启动

Enjoy
