---
title: 微信返回上一页，清空缓存
date: 2020-04-08 15:22:18
tags: 
  - 微信
---

## 微信缓存相关

### 问题描述
开发微信H5页面的时候，在IOS微信内置浏览器中点击返回按钮返回上一页时，上一页面不会被刷新。
在浏览器缓存机制中，在返回上一页的操作中， html/js/css/接口等动静态资源不会重新请求，但是js会重新加载。但在Ios微信页面中js也会保存上一页面最后执行的状态，不会重新执行js。
使用这种模式缓存机制可以加快渲染速度，但是部分数据需要经常展示和编辑的情况下回导致不同步。比如“详情页”跳转到“编辑页”，在“编辑页”中修改数据后返回到详情页中，“详情页”数据展示并未进行同步修改。

### 产生原因
+ 这里提到一个概念，浏览器前进/后退缓存，（Backward/Forward Cache，BF Cache），当然也有人叫disk Cache。
+ BF Cache 是一种浏览器优化，HTML标准并未指定其如何进行缓存，因此缓存行为是各浏览器实现不尽相同。
+ 由于不是Http缓存，所以通过头文件缓存设置no-cache是无效的。当然也不能以Http缓存机制来理解BF Cache。

### 解决思路
从网上看了几种比较常见的解决思路，下面简单讲解。

1. 设置浏览历史中当前记录
``` js
// history.replaceState方法的参数与pushState方法一模一样，区别是它修改浏览历史中当前纪录。
var href = location.href;
var time = new Date().getTime();
href += href.indexOf('?') > -1 ? ('&time='+time) : ('?time=' + time);

history.replaceState({}, "title", href);
```
比如当前页面地址为http://www.a.com; 通过history.replaceState修改后当前地址会变为http://www.a.com?time=xxx

网上这种解决思路比较多，实际情况中并不可行。

原因：Ios微信中调整到下一页面后并未将上一页面修改的url保持在历史记录中，以代码为例，返回上一页并未返回到 http://www.a.com?time=xxx ，
而是返回到 http://www.a.com 中。

2. 通过时间差来判断是否需要重置
``` js
var prev = parseInt(new Date().getTime() / 1000);
var now = prev;

window.setInterval(function() {
  now = parseInt(new Date().getTime() / 1000);
  
  // 当前步骤与上一步骤时间差超过1秒，表示页面已经跳转过
  // 时间差需要与间隔时间相对应
  if (now - prev > 1) {
    location.reload();
  } else {
    prev = now;
  }
// 间隔时间设置为1秒
}, 1000);
```
+ 原理：通过判断当前步骤与上一步骤的时间差来判断是否需要更新
+ 缺点：
间隔轮询时间差长度设置为多久比较好，不好掌控；
通过setInterval设置的间隔时间差并不是很精确；
并且两个页面之间的反复切换速度非常迅速的情况下也许会出现监测不到的现象。
3. 通过localStorage控制是否需要刷新
``` js
localStorage.setItem("need-refresh", true);
$(function () {
  var needRefresh = localStorage.getItem("need-refresh");
  if(needRefresh) {
    localStorage.removeItem("need-refresh");
    location.reload();
  }
});
```
+ 原理：通过获取浏览器保存的key来决定页面是否需要刷新
+ 缺点：当页面关闭再重新打开时，key（也就是代码中的need-refresh）key值为true，会导致页面加载两次，造成重复渲染

4. 通过pageshow事件决定是否需要刷新页面
``` js
window.addEventListener('pageshow', function(e) {
  // 通过persisted属性判断是否存在 BF Cache
  if (e.persisted) {
    location.reload();
  }
});
```
+ 原理：pageShow事件在页面显示即会触发，无论页面是否来自BF Cache。通过检测persisted属性即可判断是否存在 BF Cache 行为。
+ 优点：大部分浏览器都支持pageShow方法与persisted属性，并且需要的代码量只需要短短4行即可。
+ 缺点：每种浏览器中BF Cache的机制是不同的，部分浏览器中的Bf Cache还是会重新执行js代码，会造成重复渲染效果。当然这篇文章中我们只考虑Ios中的微信页面， 所以是不存在问题。