---
layout: post
title: 利用mkdocs搭建静态博客网站
category: 业余拓展
date: 2015-08-26
---

[官方教程](http://www.mkdocs.org/)

###源码安装

* clone项目源码 
git clone https://github.com/mkdocs/mkdocs.git
* 切换分支
git checkout fetaure/terminus
* 安装mkdocs
cd mkdocs
sudo python setup.py install
说明：MkDocs supports Python 2.6, 2.7, 3.3 and 3.4
* 在对应的docs文档的根目录下启动服务
cd /Users/songrenfei/WorkSpace/parana/docs_tech
mkdocs serve
在浏览器地址栏输入http://127.0.0.1:8000即可访问
更多命令：mkdocs --help