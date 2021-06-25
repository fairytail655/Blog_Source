---
title: Arduino 开发 ESP32
date: 2021-06-10
categories: 
- Espressif
tags:
- Arduino
---
# 环境

- Ubuntu16.04
- Arduino 1.8.15 ( >= 1.8)
- ESP32-DevKitC V4

<!--more-->

# IDE 添加 ESP32

- 参考文档 [(1)](https://github.com/espressif/arduino-esp32/blob/master/docs/arduino-ide/boards_manager.md) 和 [(2)](https://dronebotworkshop.com/esp32-intro/).
- 在 **IDE** 界面选择`File` -> `Preferences`, 在 `Additional Boards Manager URLs`中添加 **ESP32** 的链接 `https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`.
- 在 **IDE** 界面选择 `Tools` -> `Boards:...` -> `Boards Manager`, 搜索并下载 **ESP32**. 

# 编译&烧录

- 选择目标开发板
  
  在 **IDE** 界面选择 `Tools` -> `Boards:..` -> `FireBeetle-ESP32`
- 点击"Verify"按钮, 可能会报错:
  ```
  exec: "python": executable file not found in $PATH
  ```
  
  然而在 Ubuntu 终端可以直接调用 python 命令, 原因可能是终端处于 python 虚拟环境下, 但是 **PATH** 下并没有包含 python 路径.
  
  所以可以创建软连接:
  ```powershell
  ln -s 你的python路径 /usr/bin/python
  ```
  
    或者直接安装:
  ```
  sudo apt install python
  ```

- 选择对应的端口号, 点击"Upload"即可.
