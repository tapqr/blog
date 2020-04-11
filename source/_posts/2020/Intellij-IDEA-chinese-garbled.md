---
title: Intellij IDEA web项目页面中文乱码解决方案
date: 2020-04-11 21:24:58
tags: Intellij
---

## Intellij IDEA web项目页面中文乱码解决方案

下载最新的Intellij IDEA 2020.1以后，运行以前的Java Web 项目出现中文乱码的问题

### 原因：基本上是idea的tomcat或者是idea本身环境导致

### 解决方案：

选择tomcat》Edit Configurations...
![project-change-config.png](https://i.loli.net/2020/04/11/7x92Mwo8ztEqjCk.png)

选择》Startup/Connection面板》勾选Pass environment variables.》选择添加》

Name：JAVA_TOOL_OPTIONS, Value：-Dfile.encoding=UTF-8  填写完成选择右下方》Apply
![project-env-encode.png](https://i.loli.net/2020/04/11/ZgLb3UrzSB5wDmp.png)

选择》Server》在VM options》填写 -Dfile.encoding=UTF-8，填写完成选择Apply》重启TOMCAT进行调试。
![project-start-encode.png](https://i.loli.net/2020/04/11/poOwV3Yn5SsmHji.png)

如果项目启动的时候控制台输出乱码

打开tomcat目录下的conf目录，找到logging.properties文件，将下图中的UTF-8改成GBK
![tomcat-encode.png](https://i.loli.net/2020/04/11/uolzChvnFG73Sbp.png)

### tomcat更详细的修改方法参考
[https://www.cnblogs.com/wangjiming/p/11070246.html](https://www.cnblogs.com/wangjiming/p/11070246.html)
