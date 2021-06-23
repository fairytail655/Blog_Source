---
title: VSCode 下 esp-iot-solution 使用总结
date: 2021-06-23
categories: 
- Intern
tags:
- VSCode
- ESP-iot-solution
---
# 环境

- Ubuntu18.04
- VSCode 1.57.1

<!--more-->

# 步骤

## 配置环境变量

1. 修改 ~/.bashrc:
   ```
   >>> vim ~/.bashrc
   
   # 在末尾两行添加如下内容, 其中 [...] 为 esp-iot-solution 的路径.
   export IOT_SOLUTION_PATH=[...]
   ```

2. 更新并查看环境变量:
   ```
   >>> source ~/.bashrc
   >>> env | grep IOT
   ```

## 获取例程

从 **esp-iot-solution/expamples/** 下复制某一例程到指定位置. 

## 编译&烧录&调试

同 [Vscode 下 ESP-IDF 使用总结](https://fairytail655.github.io/2021/06/10/Vscode%E4%B8%8BESP-IDF%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93/).