---
layout: post
title: "memcached使用"
date: 2013-09-04 10:29:51
category: "技术"
tags: memcache
author: bingwen
---


启动

	memcached -d -m 2048  -u root -l 192.168.1.20 -p 12111 -c 1024 -P /tmp/memcached.pid 
<!--break-->
	
* -d 守护进程
* -m 内存大小 单位M 默认64M
* -p 监听端口
* -P 设置保存的pid文件

结束

	kill `/tmp/memcached.pid`
