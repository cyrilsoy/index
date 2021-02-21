---
layout: post
title: Nginx開啟目錄瀏覽配置文件
permalink: /post/nginx-autoindex.html
---

出於安全考慮，`Nginx`默認是沒有開啟目錄瀏覽的，我們搭建自己軟件倉庫的時候可能需要目錄瀏覽的支持，列出當前目錄下的所有文件，便於下載.

<!--more-->

只需要將下面幾行配置文件加入`Nginx`配置的`Server`段內：

```yml
autoindex on;                        #開啟目錄瀏覽
autoindex_exact_size off;            #顯示文件大小（單位K/M/G）
autoindex_localtime on;              #顯示修改時間
charset utf-8,gbk;                   #設置編碼
```

安裝`Nginx`

```yml
apt update
apt install nginx
```

在`/etc/nginx/sites-enabled/`創建`Nginx.conf`並填入下面代碼：

```yml
server {
	listen 443;
	listen 80;
	server_name lia.im;
	charset utf-8;
	ssl on;
	ssl_certificate /etc/nginx/ssl/ssl.crt;
	ssl_certificate_key /etc/nginx/ssl/ssl.key;
	ssl_session_timeout 5m;
	ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_prefer_server_ciphers on;
	
	location / {
	root /var/www/html;
	autoindex on;
	autoindex_exact_size off;
	autoindex_localtime on;
	}
}
```

重啟`Nginx`服務

```yml
systemctl reload nginx
```

Enjoy
