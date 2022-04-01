## ImageMagic pdf转jpg，部分图片出现黑色背景，无法正常查看问题解决
<p align='center'>2022-04-01</p>

[TOC]
### 1. 简单解决办法
转换为png格式图片
```
convert -density 120 -quality 100 test.pdf test.png
```
### 2. 必须转jpg格式，转换命令增加参数
-background white -alpha remove
```
convert -density 120 -quality 100 -background white -alpha remove test.pdf test.jpg
```
