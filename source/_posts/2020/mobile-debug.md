---
title: 移动端调试工具汇总
date: 2020-04-10 10:05:47
tags: debug
---


本帖只是汇总工作过程中用到的调试工具，不详细阐述工具使用方法。

### windows平台
##### 调试工具
1. 腾讯TBS Studio（windows系统强烈推荐）
- 下载链接[TBS Studio](https://x5.tencent.com/tbs/guide/debug/download.html)
- 下载完以后，直接安装即可使用（详细步骤官方网页有说明，软件启动过程中也会给出提示）

2. 微信专用调试工具
- [http://debugx5.qq.com/](http://debugx5.qq.com/)
- 在微信中打开以上网址,在信息标签下勾选“打开vConsole调试功能”，勾选以后，微信内打开的所有网页都会出现一个vConsole的按钮，点击此按钮，可以看到调试控制台
![16db47869e3c9ba0.png](http://q.265265.xyz/imgs/blog/yXO4vae8JVKEcpL.png)
3. Chrome浏览器的devtools

##### 抓包工具
1. fiddler
2. wireshark

### mac 平台
##### 调试工具
1. Safari
- 只能调试Safari上打开的页面
2. IPAPatch
- github地址    [https://github.com/Naituw/IPAPatch](https://github.com/Naituw/IPAPatch)
- 使用方法可以参考以下教程
    > [https://yq.aliyun.com/articles/73294](https://yq.aliyun.com/articles/73294)  
    > [https://cloud.tencent.com/developer/article/1197201](https://cloud.tencent.com/developer/article/1197201)
- hack以后的APP打开的网页可以在safari中调试，类似第1点的调试方法

##### 抓包工具
1. fiddler
2. wireshark
3. charles

### mac平台和window平台都可用
1. weinre
2. spy-debugger

### 网页调试插件
1. 腾讯出的vConsole [https://github.com/Tencent/vConsole](https://github.com/Tencent/vConsole)
2. 很二次元的console    [https://github.com/liriliri/eruda](https://github.com/liriliri/eruda)