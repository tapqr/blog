---
title: nginx常用配置
date: 2020-04-16 11:23:22
tags: nginx
cover: http://q.265265.xyz/imgs/blog/FOngofTxUR7JuLK.png
top_img: http://q.265265.xyz/imgs/blog/TBrwycbERNgZoW4.png
---

## nginx常用配置汇总

### 跨域
```nginx
location / {  
  add_header Access-Control-Allow-Origin *;
  add_header Access-Control-Allow-Methods 'GET, POST, DELETE, PUT, OPTIONS';
  add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';

  if ($request_method = 'OPTIONS') {
    return 204;
  }
}
```

### gzip
```nginx
server{
  gzip on; //启动
  gzip_buffers 32 4K;
  gzip_comp_level 6; // 压缩级别，1-10，数字越大压缩的越好
  gzip_min_length 100; // 不压缩临界值，大于100的才压缩，一般不用改
  gzip_types application/javascript text/css text/xml;
  gzip_disable "MSIE [1-6]\."; // IE6对Gzip不友好，对Gzip
  gzip_vary on;
}
```

### https
``` nginx
server {
  #ssl参数
  listen              443 ssl; // 监听443端口
  server_name         example.com;
  #证书文件
  ssl_certificate     example.com.crt;
  #私钥文件
  ssl_certificate_key example.com.key;
}
```

### 负载均衡

+ 轮询（默认）

```nginx
upstream backserver {
  server 192.168.0.1;
  server 192.168.0.2;
}
```
+ 权重weight

指定不同ip的权重，权重与访问比成正相关，权重越高，访问越大，适用于不同性能的机器

```nginx
upstream backserver {
  server 192.168.0.1 weight=2;
  server 192.168.0.2 weight=8;
}
```
+ 响应时间来分配

公平竞争，谁相应快，谁处理，不过这种方式需要依赖到第三方插件nginx-upstream-fair，需要先安装

```nginx
upstream backserver {
  server 192.168.0.1;
  server 192.168.0.2;
  fair;
}

server {
  listen 80;
  server_name localhost; 
  location / {
    proxy_pass  http://backserver;
  }
}
```
+ 健康检查

Nginx 自带 ngx_http_upstream_module（健康检测模块）本质上服务器心跳的检查，通过定期轮询向集群里的服务器发送健康检查请求,来检查集群中是否有服务器处于异常状态

如果检测出其中某台服务器异常,那么在通过客户端请求nginx反向代理进来的都不会被发送到该服务器上（直至下次轮训健康检查正常）
基本例子
```nginx
upstream backserver{
  server 192.168.0.1  max_fails=1 fail_timeout=40s;
  server 192.168.0.2  max_fails=1 fail_timeout=40s;
}

server {
  listen 80;
  server_name localhost; 
  location / {
    proxy_pass http://backend;
  }
}
```
涉及两个配置：

fail_timeout : 设定服务器被认为不可用的时间段以及统计失败尝试次数的时间段，默认为10s
max_fails : 设定Nginx与服务器通信的尝试失败的次数，默认为：1次

### 反向代理
```nginx
server {
  listen      80;
  server_name www.test.com;

  # 情形A
  # 访问 http://www.test.com/testa/aaaa
  # 后端的request_uri为: /testa/aaaa
  location ^~ /testa/ {
    proxy_pass http://127.0.0.1:8801;
  }
  
  # 情形B
  # 访问 http://www.test.com/testb/bbbb
  # 后端的request_uri为: /bbbb
  location ^~ /testb/ {
    proxy_pass http://127.0.0.1:8801/;
  }

  # 情形C
  # 下面这段location是正确的
  location ~ /testc {
    proxy_pass http://127.0.0.1:8801;
  }

  # 情形D
  # 下面这段location是错误的
  #
  # nginx -t 时，会报如下错误: 
  #
  # nginx: [emerg] "proxy_pass" cannot have URI part in location given by regular 
  # expression, or inside named location, or inside "if" statement, or inside 
  # "limit_except" block in /opt/app/nginx/conf/vhost/test.conf:17
  # 
  # 当location为正则表达式时，proxy_pass 不能包含URI部分。本例中包含了"/"
  location ~ /testd {
    proxy_pass http://127.0.0.1:8801/;   # 记住，location为正则表达式时，不能这样写！
  }

  # 情形E
  # 访问 http://www.test.com/ccc/bbbb
  # 后端的request_uri为: /aaa/ccc/bbbb
  location /ccc/ {
    proxy_pass http://127.0.0.1:8801/aaa$request_uri;
  }

  # 情形F
  # 访问 http://www.test.com/namea/ddd
  # 后端的request_uri为: /yongfu?namea=ddd
  location /namea/ {
    rewrite    /namea/([^/]+) /yongfu?namea=$1 break;
    proxy_pass http://127.0.0.1:8801;
  }

  # 情形G
  # 访问 http://www.test.com/nameb/eee
  # 后端的request_uri为: /yongfu?nameb=eee
  location /nameb/ {
    rewrite    /nameb/([^/]+) /yongfu?nameb=$1 break;
    proxy_pass http://127.0.0.1:8801/;
  }

  access_log /data/logs/www/www.test.com.log;
}

server {
  listen      8801;
  server_name www.test.com;
  
  root        /data/www/test;
  index       index.php index.html;

  rewrite ^(.*)$ /test.php?u=$1 last;

  location ~ \.php$ {
    try_files $uri =404;
    fastcgi_pass unix:/tmp/php-cgi.sock;
    fastcgi_index index.php;
    include fastcgi.conf;
  }

  access_log /data/logs/www/www.test.com.8801.log;
}
```
更加详细的方法参考[https://my.oschina.net/foreverich/blog/1512304](https://my.oschina.net/foreverich/blog/1512304)

### 常用变量
|    变量名称   |             使用方法                |
| ------------ | ----------------------------------- |
| $host | 请求信息中的Host头域值，如果请求中没有Host行，则等于设置的服务器名 |
| $http_host  | 与$host相同，但如果请求信息中没有host行，则可能不同          |
| $scheme |所用的协议 比如 http或者HTTPS |
| $request_uri |从客户端发送来的原生请求URI，包括参数|
| $args |客户端请求中的参数|
| $http_cookie  | 客户端的cookie信息          |
| $http_referer | 引用地址 |
| $http_user_agent  | 客户端代理信息 |
| $http_HEADER  | http请求信息中的HEADER字段 |
| $http_x_forwarded_for | 相当于网络访问路径 |
| $http_x_forwarded_proto | http协议类型 |

参考链接：[http://timd.cn/nginx/varindex/](http://timd.cn/nginx/varindex/)

### 缓存
```nginx
proxy_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m max_size=10g 
                 inactive=60m use_temp_path=off;

server {
  # ...
  location / {
    proxy_cache my_cache;
    proxy_pass http://my_upstream;
  }
}
```
也可以直接在location中写`expires 90d;`或者`add_header    Cache-Control  max-age=3600;`

参考链接：

[nginx官网doc](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/)

[nginx官网](https://www.nginx.com/blog/nginx-caching-guide/)

[expires和cache-control说明](https://www.cnblogs.com/kevingrace/p/10459429.html)
