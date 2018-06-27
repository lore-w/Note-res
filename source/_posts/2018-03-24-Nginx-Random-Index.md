---
title: Nginx 随机主页模块
tags:
  - nginx
categories:
  - 手记
date: 2018-03-24 15:08:48
---

# 语法规则

该模块的作用是，在目录中选择一个随机主页

```bash
Syntax: random_index on | off;
# 默认没有开启
Default: random_index off;
# 需要配置在location模块下
Context: location
```

<!-- more -->

# 栗子来了

```bash
$ cd /etc/nginx/conf.d
$ vim default.conf

server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        # 步骤：
        # 先打开模块
        # 把index注释
        # 注意：该模块不会选择隐藏文件作为随机主页
        random_index on;
        #index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
    ...
}

$ wq
...
# 检查配置文件的语法和路径是否正确
$ nginx -t -c /etc/nginx/conf.d/default.conf

nginx: [emerg] "server" directive is not allowed here in /etc/nginx/conf.d/default.conf:1
nginx: configuration file /etc/nginx/conf.d/default.conf test failed

# 注意，这里要检查的不是子配置文件，而是/etc/nginx下的nginx.conf配置文件
nginx -t -c /etc/nginx/nginx.conf

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

# 重载服务
$ nginx -s reload -c /etc/nginx/nginx.conf

# 检查/usr/share/nginx/html下是否有多个html文件
$ cd /usr/share/nginx/html
$ ls
50x.html  index.html

# 获取本机IP
$ ip a

...
# 注意这里获取的是阿里云的私有IP，我们需要公网IP
inet 172.xxx.xxx.xxx/20 brd 172.xxx.xxx.xxx scope global dynamic eth0
...

# 多次请求
$ curl http://47.97.112.40
$ curl http://47.97.112.40
$ curl http://47.97.112.40
```