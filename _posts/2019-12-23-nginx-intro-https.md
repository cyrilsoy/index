---
layout: post
title: Nginx 配置 HTTPS A+
permalink: /post/nginx-intro-https.html
---

![Nginx](https://cdn.jsdelivr.net/gh/cyrilsoy/hello-blog@main/static/nginx-intro-https/nginx-intro-https-1.png)

<!--more-->

测试地址：[https://www.ssllabs.com/ssltest/](https://www.ssllabs.com/ssltest/?utm_source=textarea.com&utm_medium=textarea.com&utm_campaign=article)

nginx 配置，只贴出 SSL 相关，需要将配置放到 server {} 位置。

首先开启 ssl

```yml
listen 443 ssl;
server_name www.example.com;
ssl on;
ssl_certificate /etc/ssl/certs/ssl-bundle.crt;
ssl_certificate_key /etc/ssl/private/www_example_com.key;
```

其中 ssl-bundle.crt 是网站证书，www_example_com.key 是证书私钥，如何获取这两个文件，请自行搜索。

需要注意的是，大部分 CA 提供的证书都是多级，所以可能需要我们把多个证书合并成一个，这样可以减少浏览器额外下载中间证书的次数。

生成 dhparam.pem

```yml
$ openssl dhparam -out dhparam.pem 4096
```

配置到 nginx

```yml
ssl_dhparam /etc/ssl/certs/dhparam.pem;
```

协议和 ciphers 选择，ciphers 的选择比较关键，这个配置中的 ciphers 支持大多数浏览器，但不支持 XP/IE6 。

```yml
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_stapling on;
ssl_ciphers "ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA";
ssl_prefer_server_ciphers on;
```

ssl session 配置

```yml
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

HSTS 配置，这个对评分影响也比较大，但如果开启这个，需要全站开启 HTTPS 。

```yml
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
```

完整配置文件

```yml
server {
        listen 443 ssl;
        server_name www.example.com;
        ssl on;
        ssl_certificate /etc/ssl/certs/ssl-bundle.crt;
        ssl_certificate_key /etc/ssl/private/www_example_com.key;
        ssl_dhparam /etc/ssl/certs/dhparam.pem;
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_stapling on;
        ssl_ciphers "ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA";
        ssl_prefer_server_ciphers on;
        add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
        location / {
                # pass
        }
}
```

更新：

请移步 [Mozilla SSL Configuration Generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/?utm_source=textarea.com&utm_medium=textarea.com&utm_campaign=article)

## 转载说明

[分享一个 HTTPS A+ 的 nginx 配置](https://www.textarea.com/zhicheng/fenxiang-yige-https-a-di-nginx-peizhi-320/)

Enjoy
