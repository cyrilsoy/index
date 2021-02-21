---
layout: post
title: Debian 9 / Debian 8 使用源安装 LEMP 教程
permalink: /post/debian-install-nginx-php-mysql.html
---

Debian 9 代号 [Stretch](https://www.debian.org/releases/stretch/) 在 2017 年 6 月 18 号正式发布，由于是最新的系统，原来基于 Dotdeb 安装最新 LEMP 的教程已经过时，本文将介绍使用官方源和第三方源在 Debian 9.x Stretch 安装最新版 LEMP 的教程，并且可以自行选择 PHP 版本。

<!--more-->

**PS：本文同时适用于 Debian 8.x Jessie 如果您的服务商暂未提供 Debian 9.x 也可以凑合一用**

以下操作推荐在 root 用户下完成，请使用 `sudo -i` 切换到 root 用户进行操作

## 更新系统并安装部分必要软件

```yml
sudo apt-get update && apt-get upgrade
sudo apt-get install curl vim wget unzip apt-transport-https lsb-release ca-certificates
```

**如果你通过 iso 方式安装 Debian 9 并且设置了 root 密码，则默认不带 `sudo` 包，使用 `apt-get install sudo` 安装即可**

当然您也可以加入 Backports 源，方便安装更新的软件

```yml
sudo cat >> /etc/apt/sources.list.d/backports.list << EOF
deb http://deb.debian.org/debian $(lsb_release -sc)-backports main
deb-src http://deb.debian.org/debian $(lsb_release -sc)-backports main
EOF
```

国内的机器可以用[中科大](https://mirrors.ustc.edu.cn/)的源

```yml
sudo cat >> /etc/apt/sources.list.d/backports.list << EOF
deb https://mirrors.ustc.edu.cn/debian $(lsb_release -sc)-backports main
deb-src https://mirrors.ustc.edu.cn/debian $(lsb_release -sc)-backports main
EOF
```

然后执行升级

```yml
sudo apt-get -t stretch-backports update && apt-get -t stretch-backports upgrade
```

## 增加 Ondřej Surý 大神打包的 Nginx 源并安装

这里我们推荐 [Ondřej Surý](https://deb.sury.org/) 大神打包的 Nginx 源，这货是一个来自捷克的大佬

### 首先增加 Key

```yml
sudo wget -O /etc/apt/trusted.gpg.d/nginx-mainline.gpg https://packages.sury.org/nginx-mainline/apt.gpg
```

### 然后增加 Nginx 源

```yml
sudo cat >> /etc/apt/sources.list.d/nginx.list << EOF
deb https://packages.sury.org/nginx-mainline/ $(lsb_release -sc) main
EOF
```

国内机器可以用 [xTom](https://xtom.com.hk/) 的香港源

```yml
sudo cat >> /etc/apt/sources.list.d/nginx.list << EOF
deb https://mirror.xtom.com.hk/sury/nginx-mainline/ $(lsb_release -sc) main
EOF
```

### 接着更新并接安装 Nginx

```yml
sudo apt-get update
sudo apt-get install nginx-extras
```

安装完毕后，我们可以使用 `nginx -v` 命令看到 Nginx 已经是最新的 1.13.3 主线版了

```yml
root@debian9 ~ # nginx -v
nginx version: nginx/1.13.5
```

### 注意事项

Debian 9 的 Backports 也有小伙伴在更新 Nginx，但是更新速度慢，然而由于 Backports 优先级较高，万一两边版本号一样的时候，会默认更新 Backports 仓库，所以我们有必要把 Backports 的优先级降低

```yml
sudo cat >> /etc/apt/preferences << EOF
Package: nginx*
Pin: release a=stretch-backports
Pin-Priority: 499
EOF
```

另外默认没有设置系统启动，需要我们手工加入

```yml
sudo systemctl enable nginx
```

## 增加 Ondřej Surý 大神打包的 PHP 源并安装 PHP 7.x

[Ondřej Surý](https://deb.sury.org/) 大佬打包的 PHP 源更是好用，Ubuntu 的 [PPA for PHP](https://launchpad.net/~ondrej/+archive/ubuntu/php) 就是这位大佬做的，当然少不了 Debian 的源了，下面一步一步来

### 增加大神的 PGP

```yml
sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
```

### 加入大神做好的源

```yml
sudo sh -c 'echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
```

国内机器可以用 [xTom](https://xtom.com.hk/) 的香港源

```yml
sudo sh -c 'echo "deb https://mirror.xtom.com.hk/sury/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
```

### 更新系统

```yml
sudo apt-get update
```

### 安装自己需要的 PHP 版本

这个源目前默认的 PHP 是 7.1.x ，如果您需要 7.2.x 或 7.0.x 或 5.6.x 那么请修改对应的 PHP 版本号（注意配置文件哦）

这里举例 WordPress 需要的部分 PHP 包

安装 PHP 7.2.x ( PHP 7.2 开始已经不支持 mcrypt 组件 )

```yml
sudo apt-get install php7.2-fpm php7.2-mysql php7.2-curl php7.2-gd php7.2-mbstring php7.2-xml php7.2-xmlrpc php7.2-zip php7.2-opcache
```

对应 PHP 7.1.x 就是

```yml
sudo apt-get install php7.1-fpm php7.1-mysql php7.1-curl php7.1-gd php7.1-mbstring php7.1-mcrypt php7.1-xml php7.1-xmlrpc php7.1-zip php7.1-opcache
```

对应 PHP 7.0.x 就是

```yml
sudo apt-get install php7.0-fpm php7.0-mysql php7.0-curl php7.0-gd php7.0-mbstring php7.0-mcrypt php7.0-xml php7.0-xmlrpc php7.0-zip php7.0-opcache
```

对应 PHP 5.6.x 就是

```yml
sudo apt-get install php5.6-fpm php5.6-mysql php5.6-curl php5.6-gd php5.6-mbstring php5.6-mcrypt php5.6-xml php5.6-xmlrpc php5.6-zip php5.6-opcache
```

如果希望安装其他组件，可以通过搜索看看有没有对应的包

```yml
sudo apt-cache search php7.2* | grep php
```

修改 `php.ini` 防止跨目录攻击，如果安装的 PHP 7.1.x 请相应修改 `/etc/php/7.1/fpm/php.ini` PHP 7.0.x 请相应修改 `/etc/php/7.0/fpm/php.ini` PHP 5.6.x 请修改 `/etc/php/5.6/fpm/php.ini`

```yml
sudo sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/' /etc/php/7.2/fpm/php.ini
```

### 重启 PHP 和 Nginx

```yml
sudo systemctl restart php7.2-fpm
```

对应 PHP 7.1.x 命令如下

```yml
sudo systemctl restart php7.1-fpm
```

对应 PHP 7.0.x 命令如下

```yml
sudo systemctl restart php7.0-fpm
```

对应 PHP 5.6.x 命令就是

```yml
sudo systemctl restart php5.6-fpm
```

Nginx 参考配置文件如下，新建立个 `/etc/nginx/sites-enabled/example.com.conf`

```yml
sudo cat >> /etc/nginx/sites-enabled/example.com.conf << EOF
server {  
        listen 80;
        listen [::]:80;

# 指定网站目录，可根据自己情况更换，建议放在 /var/www 目录下
        root /var/www/example.com;
        index index.php index.html index.htm;

# 默认第一个域名，替换 example.com 为您的域名
        server_name example.com;

        location / {
            try_files \$uri \$uri/ =404;
        }

# 开启 PHP7.2-fpm 模式，如需要安装 PHP 7.1.x 请修改为 fastcgi_pass unix:/run/php/php7.1-fpm.sock;
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php7.2-fpm.sock;
        }
}
EOF
```

到这里基本没有问题，可以直接重启 Nginx

```yml
sudo systemctl restart nginx
```

我们的目录在 `/var/www/example.com`， 创建一个 `phpinfo.php` 并输入 `phpinfo()` 函数

```yml
sudo cat >> /var/www/example.com/phpinfo.php << EOF
<?php phpinfo(); ?>
EOF
```

好了，此时在浏览器输入 `http://example.com/phpinfo.php`，如果看到经典的 `phpinfo` 页面则说明安装成功，如果不成功，请仔细对比步骤查找哪里出错或在烧饼博客下方留言

效果如下

![Debian Install LEMP PHPINFO](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/debian-install-nginx-php-mysql/debian-lemp-phpinfo.png)

## 安装 MySQL 5.7.x / Percona Server 5.7.x

Debian 9.x Stretch 默认[已经使用](https://mariadb.com/resources/blog/mariadb-server-default-debian-9) Mariadb ，所以这里我们推荐 [Percona Server](https://www.percona.com/downloads/Percona-Server-LATEST/)， 这货已经发布了 Debian 9.x Stretch 版本

### 首先，导入 Percona Server 的官方源

按照官方的[教程](https://www.percona.com/doc/percona-server/LATEST/installation/apt_repo.html)导入即可

```yml
sudo wget https://repo.percona.com/apt/percona-release_0.1-5.$(lsb_release -sc)_all.deb
sudo dpkg -i percona-release_0.1-5.$(lsb_release -sc)_all.deb
```

然后查看一下对应的 `/etc/apt/sources.list.d/percona-release.list` 文件是否正确

```yml
root@debian9 ~ # cat /etc/apt/sources.list.d/percona-release.list 
#
# Percona releases, stable
#
deb http://repo.percona.com/apt stretch main
deb-src http://repo.percona.com/apt stretch main
```

国内机器可以用 [xTom](https://xtom.com.hk/) 的香港源

```yml
sudo sed -i 's/repo.percona.com/mirror.xtom.com.hk\/percona/' /etc/apt/sources.list.d/percona-release.list
```

也可以用清华大学的源

```yml
sudo sed -i 's/repo.percona.com/mirrors.tuna.tsinghua.edu.cn\/percona/' /etc/apt/sources.list.d/percona-release.list
```

### 接着更新一下系统

```yml
sudo apt-get update
```

### 然后直接安装最新版 MySQL 5.7.x / Percona Server 5.7.x

```yml
sudo apt-get install percona-server-server-5.7
```

在弹出的界面里输入两次随机并且强大的 MySQL root 密码即可，安装成功后检查一下版本

```yml
root@debian9 ~ # mysql -V
mysql  Ver 14.14 Distrib 5.7.18-16, for debian-linux-gnu (x86_64) using  7.0
```

安装完毕后强烈推荐使用 `sudo mysql_secure_installation` 命令做一次安全设置，详见 [Ubuntu Server 16.04.x (Xenial Xerus) 安装 LEMP / LNMP 教程](https://cyrilsoy.com/ubuntu-server-16-04-install-nginx-php-mysql.html) 里面的说明，同样其他的配置都是一样的

### 创建数据库并测试

使用 MySQL root 用户登陆

```yml
sudo mysql -u root -p
```

创建数据库 `example_database`

```yml
CREATE DATABASE example_database DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

创建用户名 `example_user` 并赋予权限

```yml
GRANT ALL ON example_database.* TO 'example_user'@'localhost' IDENTIFIED BY '这里改成你要设置的强大的没人能猜出来的随机的密码';
```

刷新 MySQL 权限

```yml
FLUSH PRIVILEGES;
```

没问题以后就可以退出了

```yml
EXIT;
```

新建立一个 `/var/www/example.com/mysql-test.php` 文件并测试

```yml
sudo cat >> /var/www/example.com/mysql-test.php << EOF  
<?php  
\$dbname = 'example_database';    //MySQL 数据库名
\$dbuser = 'example_user';   //MySQL 用户名
\$dbpass = '你的强大的没人可以猜出来的密码';
\$dbhost = 'localhost';  //安装在本地就用 localhost
\$link = mysqli_connect(\$dbhost, \$dbuser, \$dbpass) or die("Unable to Connect to '\$dbhost'");
mysqli_select_db(\$link, \$dbname) or die("Could not open the db '\$dbname'");  
\$test_query = "SHOW TABLES FROM \$dbname";
\$result = mysqli_query(\$link, \$test_query);
\$tblCnt = 0;
while(\$tbl = mysqli_fetch_array(\$result)) {  
  \$tblCnt++;
  #echo \$tbl[0]."&lt;br /&gt;\n";
}
if (!\$tblCnt) {  
  echo "MySQL is working fine. There are no tables. More information on https://sb.sb/tag/mysql/";
} else {
  echo "MySQL is working fine. There are \$tblCnt tables. More information on https://sb.sb/tag/mysql/";
}
?>
EOF
```

创建完毕后访问 `http://example.com/mysql-test.php` 如果出现 `MySQL is working fine. There are no tables.` 则说明 MySQL 工作正常。

好了，以上就是基本的 Debian 9.x “Stretch” 安装最新版 LEMP 的教程，如有问题可以随时发评论留言讨论。

Enjoy.
