---
title: Linux常用命令总结
date: 2021-06-04
categories: 
- Intern
tags:
- Linux
---
- 目录切换 `cd`
- 目录创建 `mkdir`
- 查看目录内容 `ls [-l|a] [dir]`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190428140017647.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Jvb2tpZXhpYW9NdV9h,size_16,color_FFFFFF,t_70)

<!--more-->

- 权限修改 `chmod [a|u|g|o][+|-|=][r|w|x|-] file` 

- 文件搜索 `find [dir] [-name file/dir]`

- 文件重命名/移动 `mov`

- 复制 `cp [-r] src des`

- 文件内容显示

  ```shell
  cat file		# 显示文件所有的内容
  more file		# 显示一屏就暂停
  less file		# 向前向后翻页显示文件
  head [-n num] file 		# 显示文件前num(默认10)行
  tail [-n num] file		# 显示文件后num(默认10)行
  ```

- 创建文件 `touch`

- 文件压缩/解压

  ```shell
  tar [options] des src
  [options]: 	-c	压缩
  			-x	解压
  			-v  显示过程详细信息
  			-j	bzip2
  			-z	gzip
  examples:
  tar -cjf test.tar.bz2 test	# 将 test 压缩成 test.tar.bz2
  tar -xjf test.tar.bz2		# 将 test.tar.bz2 解压为 test
  tar -czf test.tar.gz test	# 将 test 压缩成 test.tar.gz
  tar -xzf test.tar.gz		# 将 test.tar.gz 解压为 test
  ```

- 查看当前路径 `pwd`

- 查看进程 `ps [a|u|x]`

- 关闭进程 `kill -9 process_id`

- 通过管道查找字符串 `ll | grep test`

- 关机 `shutdown -h now`

- 重启 `reboot`