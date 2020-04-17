---
title: nginx问题汇总
date: 2020-04-13 22:50:21
updated: 2020-04-17 14:15:21
tags: nginx
cover: https://i.loli.net/2020/04/16/FOngofTxUR7JuLK.png
top_img: https://i.loli.net/2020/04/16/TBrwycbERNgZoW4.png
---
## 记录开发过程中遇到的nginx配置问题
### net err_http2_protocol_error
最近在上线的时候碰到一个奇怪的问题，打开页面的时候，部分js出现`net err_http2_protocol_error`的错误，而直接打开链接是可以访问的。

原因：上线的时候修改了nginx的启动用户，导致nginx读取缓存目录的时候没有权限

解决方法：改回nginx的启动用户或者在server中配置`proxy_max_temp_file_size 0;`（强制nginx不使用缓存）

### 微信页面缓存

微信中打开的页面，如果要微信不适用缓存，需要手动在http头中加入不适用缓存的信息

以下是vue单页应用中使用到的nginx配置

```nginx
map $sent_http_content_type $var_last_modified {
  default         $sent_http_last_modified;
  text/html       $date_gmt;
}

map $sent_http_content_type $var_cache_control {
  default         'max-age=2592000';
  text/html       'no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0';
}

location / {
  add_header Last-Modified $var_last_modified;
  add_header Cache-Control $var_cache_control;
}
       	
```