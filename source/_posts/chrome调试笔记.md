---
title: chrome调试笔记
tags:
  - jQuery
  - Chrome
categories:
  - 技术
date: 2018-01-27 16:57:02
---

主要包含Chrome浏览器一些调试方法
<!--more-->
## 使用jQuery语法
如果在页面没有使用jquery库的时候，在chrome的console控制台只能使用$选择器，不能使用jquery的方法。为了调试方便，可以手动的为页面引入jquery库。

在控制台输入以下代码片段：
```bash
var jq = document.createElement('script');  
jq.src = "http://apps.bdimg.com/libs/jquery/1.8.3/jquery.min.js"; 
document.getElementsByTagName('head')[0].appendChild(jq);  
jQuery.noConflict(); //解决库冲突
```