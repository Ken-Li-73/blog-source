---
title: 解决next主题搜索栏被遮挡的原因
tags:
  - next
comments: true
abbrlink: d62d9cd6
date: 2021-11-17 14:06:47
---

使用next主题美化博客，当打开本地搜索功能时，发现搜索栏被遮挡在下面。

1. 修改属性将搜索栏置顶

<!--more-->

这是next主题CSS的问题，可以使用`z-index`属性来调节样式。

打开根目录下`./_config.next.syml`文件(老版本文件在`./themes/next/_config.syml`)找到`custom_file_path:`修改为：

```yml
custom_file_path:
  style: source/_data/styles.styl
```



打开根目录下`./source/_data/styles.styl`文件。找到`.head-inner属性`添加`z-index: 9999;`(没有就自己创建)

```stylus
.header-inner {
  z-index: 9999;
}
```

重新构建页面，问题解决。

![](result.jpg)
