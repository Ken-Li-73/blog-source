---
title: 解决next主题搜索栏被遮挡的原因
tags:
  - next
comments: true
abbrlink: d62d9cd6
date: 2021-11-17 14:06:47
---

使用next主题美化博客，当打开本地搜索功能时，发现搜索栏被遮挡在下面。

<!--more-->

这是next主题CSS的问题，可以使用`z-index`属性来调节样式。打开根目录下`/themes/next/source/css/_common/outline/header/header.styl`文件。找到`.head-inner`添加`z-index: 9999;`

```stylus
.header-inner {
  margin: 0 auto;
  width: $content-desktop;
  z-index: 9999;
  +desktop-large() {
    width: $content-desktop-large;
  }
```

重新构件页面，问题解决。

![](result.jpg)
