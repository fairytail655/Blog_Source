---
title: Eclipse 下 esp-iot-solution 使用总结
date: 2021-06-23
categories: 
- Espressif
tags:
- Eclipse
- ESP-iot-solution
---
# 环境

- Ubuntu18.04 / Windows7
- Eclipse 2021-03 (已安装 ESP-IDF 插件)

<!--more-->

# 步骤

## 获取例程

从 **esp-iot-solution/expamples/** 下复制某一例程到指定位置. 

## 获取工程文件

1. 通过 `File` -> `New` -> `Project...` -> `Espressif IDF Project` 新建工程, 指定任意路径, 命名为 **test**.
2. 进入工程 **test** 的根目录, 可以看到 Eclipse 的工程描述文件 `.project` 和 `.cproject`(如果没有则需要开启显示隐藏文件的功能), 复制它们到例程根目录下.
3. 此后不再需要工程 **test** , 可以删除.
4. 修改例程的 `.project` 文件, 将工程名 ***test*** 替换为例程名(也可以不修改, 不会报错).

```
<name>test</name>         # 原文件
<name>例程名</name>       # 修改后
```

## 在 Eclipse 中打开例程

通过 `File` -> `Import` -> `Espressif` -> `Existing IDF Project` 导入例程, 无需填写工程名, 指定工程路径即可.

## 编译&烧录&调试

1. 进入 `Window` -> `Preferences` -> `C/C++` -> `Build` -> `Environment`, 添加环境变量 **IOT_SOLUTION_PATH**.

```
Name: IOT_SOLUTION_PATH
Value: 复制 esp-iot-solution 的路径, 不要点 Variables.
```

2. 同 [Eclipse 下 ESP-IDF 使用总结](https://fairytail655.github.io/2021/06/10/Eclipse%E4%B8%8BESP-IDF%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93/).

# FAQ

- 编译报错如下:
  ```
  CMake Warning at CMakeLists.txt:9 (message):
    Can't detect IOT_SOLUTION_PATH in your environment, we infer it is
    ... # 某个路径
  
  
  CMake Error at CMakeLists.txt:12 (include):
    include could not find load file:
    ... # 某个路径
  ```
  
  解决方法: 按照前面 **编译&烧录&调试** 小节第 1 步来配置环境变量.
