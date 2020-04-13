---
title: nginx 相关的配置
date: 2020-04-13 22:50:21
tags: nginx
---

最近在上线的时候碰到一个奇怪的问题，打开页面的时候，部分js出现`net err_http2_protocol_error`的错误，而直接打开链接是可以访问的。

原因：上线的时候修改了nginx的启动用户，导致nginx读取缓存目录的时候没有权限

解决方法：改回nginx的启动用户或者在server中配置`proxy_max_temp_file_size 0;`（强制nginx不使用缓存）

