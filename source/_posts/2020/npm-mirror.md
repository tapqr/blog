---
title: 修改npm镜像
date: 2020-04-10 10:07:33
tags: npm
cover: http://q.265265.xyz/imgs/blog/cYloKC96hUVBjyd.png
top_img: http://q.265265.xyz/imgs/blog/cYloKC96hUVBjyd.png
---

### 修改npm源
+ 在项目跟目录或者用户根目录下新增 `.npmrc` 文件
```
registry = 镜像地址（https://registry.npm.taobao.org）

也可以指定某个包使用特定的镜像地址
@sentry:registry = 镜像地址（https://registry.npm.taobao.org）
```
+ 单次修改npm镜像源
```
npm --registry https://registry.npm.taobao.org install express
```

+ 修改npm默认源
```
npm config set registry https://registry.npm.taobao.org
```
+ 使用第三方工具cnpm

cnpm 使用说明: [http://npm.taobao.org/](http://npm.taobao.org/)

### 修改完镜像源以后可能还会存在安装不了的情况

原因：有些npm 包安装过程中会去github或者其他被枪的网站下载zip包,例如node-sass,electron

##### 以electron为例
electron会download一个electron-download的npm包，然后执行`node install.js`(图1)，从github release page下载electron zip包（图2）

<p align="center">
  <img src="http://q.265265.xyz/imgs/blog/gz5F1kOUuy2jeRf.png" alt="png"/>
</p>
<center>图1</center>

![16dd3db65df8223c.png](http://q.265265.xyz/imgs/blog/mAFSUCvWbs51rlz.png)
<center>图2</center>

解决方法：
+ 在.npmrc 中文件中添加
```
disturl=https://npm.taobao.org/dist
```
然后在命令行添加对应的环境变量
electron 和 sass的镜像分别为：
```
export ELECTRON_MIRROR="https://npm.taobao.org/mirrors/electron/"
export SASS_BINARY_SITE="https://npm.taobao.org/mirrors/node-sass"
```

这里涉及到一个概念，npm安装部分包的时候需要用到node-gyp实时编译

要理解node-gyp首先要知道什么是gyp(https://gyp.gsrc.io/index.md)。gyp其实是一个用来生成项目文件的工具，一开始是设计给chromium项目使用的，后来大家发现比较好用就用到了其他地方。生成项目文件后就可以调用GCC, vsbuild, xcode等编译平台来编译。至于为什么要有node-gyp，是由于node程序中需要调用一些其他语言编写的工具甚至是dll，需要先编译一下，否则就会有跨平台的问题，例如在windows上运行的软件copy到mac上就不能用了，但是如果源码支持，编译一下，在mac上还是可以用的。