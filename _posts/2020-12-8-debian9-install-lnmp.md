---
layout: post
title: Debian9详细手动安装LNMP环境
permalink: /post/debian9-install-lnmp.html
---

本次搭建的环境各个软件版本号分别为：

Debian9（Linode）
Nginx：1.14.0
MySQL：8.0（据说性能提升了N倍）
PHP：7.0.30

Debian9默认内核应该都是4.9以上了，所以可以在开始之前先开一波BBR，优化下网络：

```yml
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```

现在我们开始安装Nginx，Debian9的源默认只能安装到1.10版本，该版本较旧，所以我们自己添加源安装新的稳定版本：

```yml
nano /etc/apt/sources.list
```

写入：

```yml
deb http://nginx.org/packages/debian/ stretch nginx
deb-src http://nginx.org/packages/debian/ stretch nginx
```

添加key防止安装的时候出现GPG签名报错：

```yml
wget http://nginx.org/keys/nginx_signing.key
apt-key add nginx_signing.key
```

安装：

```yml
apt-get update
apt-get install nginx
```

接着来安装MySQL，MySQL最近的动作有点大哟，直接从5.0版本跨度到了8.0，据说8.0的版本性能有非常大的提升（虽然我没感觉出来233）。

先下载deb包：

```yml
wget https://dev.mysql.com/get/mysql-apt-config_0.8.10-1_all.deb
```

安装deb包：

```yml
dpkg -i mysql-apt-config_0.8.10-1_all.deb
```

这里会弹出窗口问你要安装的版本和一些其他的东西，这里我直接默认OK：

![debian9-install-lnmp](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/debian9-install-lnmp/debian9-install-lnmp-1.png)

完事之后我们直接apt安装：

```yml
apt-get update
apt-get install mysql-server
```

然后会提示让你设置ROOT密码：

![debian9-install-lnmp](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/debian9-install-lnmp/debian9-install-lnmp-2.png)

接着到了非常关键的地方了，这个地方一定要选择如下图圈起来的选项：

![debian9-install-lnmp](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/debian9-install-lnmp/debian9-install-lnmp-3.png)

注：8.0版本采用了新的密码加密机制，但这个机制目前太过于“前卫”，导致很多程序目前都不兼容，所以这块我们只能委曲求全选择兼容旧版。

最后我们来安装PHP7，因为Debian9源内自带了目前的PHP7稳定版，所以可以直接apt安装：

```yml
apt-get install php7.0 php7.0-cli php7.0-common php7.0-curl php7.0-fpm php7.0-gd php7.0-mysql php7.0-opcache php7.0-xml php7.0-xmlrpc php7.0-sqlite3 php7.0-mbstring
```

安装完成之后记得启动一下FPM：

```yml
systemctl start php7.0-fpm
```

注：这个7.0.30的FPM监听方式已经从之前的127.0.0.1:9000改为了/run/php/php7.0-fpm.sock。所以接下来我们在编辑Nginx配置文件的时候需要注意一下。

环境现在已经都安装好了，有个小坑需要填一下，编辑Nginx的主配置文件：

```yml
nano /etc/nginx/nginx.conf
```

把运行用户改为root：

```yml
user  root;
```

如图所示：

![debian9-install-lnmp](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/debian9-install-lnmp/debian9-install-lnmp-4.png)

现在来测试一下我们搭建的这个环境是否正常，是否可用！

先来安装一个phpMyAdmin：

```yml
apt-get install unzip
cd /usr/share/nginx/
wget https://files.phpmyadmin.net/phpMyAdmin/4.8.2/phpMyAdmin-4.8.2-all-languages.zip
unzip phpMyAdmin-4.8.2-all-languages.zip
rm -rf phpMyAdmin-4.8.2-all-languages.zip
mv phpMyAdmin-4.8.2-all-languages phpmyadmin
```

新建一个Nginx配置文件：

```yml
nano /etc/nginx/conf.d/phpmyadmin.conf
```

写入：

```yml
server {
    listen       1111;
    server_name  1.1.1.1;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/phpmyadmin;
        index  index.html index.htm index.php;
    }

    location ~ \.php$ {
        root           /usr/share/nginx/phpmyadmin;
        fastcgi_pass   unix:/run/php/php7.0-fpm.sock;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/phpmyadmin$fastcgi_script_name;
        include        fastcgi_params;
    }

}
```

然后重启Nginx：

```yml
systemctl restart nginx
```

再来安装一个WordPress：

```yml
cd /usr/share/nginx/
wget https://cn.wordpress.org/wordpress-4.9.4-zh_CN.zip
unzip wordpress-4.9.4-zh_CN.zip
rm -rf wordpress-4.9.4-zh_CN.zip
```

新建一个Nginx配置文件：

```yml
nano /etc/nginx/conf.d/wordpress.conf
```
```yml
server {
    listen       81;
    server_name  1.1.1.1;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/wordpress;
        index  index.html index.htm index.php;
        include /etc/nginx/conf.d/rewrite/wordpress.conf;
    }

    location ~ \.php$ {
        root           /usr/share/nginx/wordpress;
        fastcgi_pass   unix:/run/php/php7.0-fpm.sock;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/wordpress$fastcgi_script_name;
        include        fastcgi_params;
    }

}
```
再新建一个WordPress的伪静态配置文件：

```yml
mkdir -p /etc/nginx/conf.d/rewrite/
nano  /etc/nginx/conf.d/rewrite/wordpress.conf
```

写入伪静态规则：

```yml
location / {
	try_files $uri $uri/ /index.php?$args;
}
```

重要！最后把wordpress的目录所有者改为www-data（在Debian9中，PHP默认以www-data用户运行）

```yml
chown -R www-data:www-data wordpress
```

你可能会用到的配置文件路径：

Nginx错误日志：/var/log/nginx/error.log
Nginx主配置文件：/etc/nginx/nginx.conf
Nginx站点配置文件：/etc/nginx/conf.d/
Nginx站点目录：/usr/share/nginx/
PHP-FPM配置文件：/etc/php/7.0/fpm/pool.d/www.conf
PHP.INI配置文件：/etc/php/7.0/fpm/php.ini
MySQL主配置文件：/etc/mysql/my.cnf

如果不想用这个环境了，想删除环境：

```yml
apt-get autoremove --purge nginx
apt-get autoremove --purge mysql-server
```

此次在Debian9上装LNMP让我引起舒适的地方有三个：

1.BBR直接开，舒服！
2.PHP直接装，舒服！
3.全程就只有一个Nginx的权限问题，坑少，解决也快~

## 转载说明

[Debian9详细手动安装LNMP环境](https://lala.im/3894.html)

Enjoy