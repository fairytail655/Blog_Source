---
title: Vscode 下 ESP-IDF 使用总结
date: 2021-06-10
categories: 
- Intern
tags:
- IDE
- IDF
---
# 环境

- Ubuntu 18.04.5 LTS / Windows 7
- VScode 1.56.2
- ESP32-S2-Kaluga-Kit

<!--more-->

# IDE 安装

下载安装包后安装即可.

# IDF 环境搭建

- 在 **VScode** 插件市场下载插件 **Espressif IDF**.
- 安装好 [espressif/vscode-esp-idf-extension](https://github.com/espressif/vscode-esp-idf-extension) 中 ***Prerequisites*** 相应平台的软件依赖.
- 按下 **F1** 打开命令行, 输入 `ESP-IDF: Configure ESP-IDF extension`, 选择合适的初始化模式
  ```
  - EXPRESS             // 快速安装, 适合快速入门
  - ADVANCED            // 自定义配置
  - USE EXISTING SETUP  // 使用已有 ESP-IDF
  ```
- **Select download server:** 选择 **Espressif** 相对 **Github** 更快。

## Windows

<span style="color:red">通过[文档](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32s2/get-started/index.html#get-started-set-up-env)中"第四步:设置环境变量", 可能无法将 ESP-IDF 工具的路径加入系统环境变量 **PATH**, 所以需要人为确认是否成功设置了环境变量. 如果设置失败, 可以手动将命令执行窗口中输出的路径添加到 **PATH** 中.</span>

# 新建工程

- 参考[文档](https://github.com/espressif/vscode-esp-idf-extension/blob/master/docs/tutorial/basic_use.md).

## 基于例程

- 命令行输入 `ESP-IDF: Show Examples Projects`, 选择当前 ESP-IDF, 出现的界面显示了所有的例程.
- 选择 `blink` 并点击 `Create project using example blink`, 指定保存目录后会自动切换到工程.

# 配置&编译&烧录

- 命令行输入 `ESP-IDF: Set Espressif device target`, 根据实际芯片设置.
- 命令行输入 `ESP-IDF: SDK Configuration editor`, 可以对工程进行配置.(VScode 左下角有快捷图标)
- 命令行输入 `ESP-IDF: Build your project`, 编译工程.(VScode 左下角有快捷图标)
- 命令行输入 `ESP-IDF: Size analysis of the binaries`, 可以对二进制文件进行内存分析.
- 命令行输入 `ESP-IDF:Device Configuration`, 根据目标开发板对 "Openocd Config files" 进行修改, 如:
  ```
  # 对应于 ESP32-S2-Kaluga-Kit 开发板
  interface/ftdi/esp32s2_kaluga_v1.cfg,board/esp32s2-kaluga-1.cfg
  ```
- 命令行输入 `ESP-IDF: Flash your project`, 执行烧录.(VScode 左下角有快捷图标)
- 命令行输入 `ESP-IDF: Monitor your device`, 打开串口监视器.(VScode 左下角有快捷图标)

# 调试

- [参考文档](https://github.com/espressif/vscode-esp-idf-extension/blob/master/docs/tutorial/debugging.md)

- 按下 **F5** 开启调试.

## Ubuntu

<span style="color:red">遇到的问题</span>:

- 有时开启会报错, 但再次开启可以正常运行.
- 在不设断点的情况下, 点击"继续"按钮后调试功能卡死.
- 在设置一个断点的情况下, 点击"继续"按钮后能够在该处停止运行, 但再次点击后,程序不能在断点处停止, 此后程序运行异常.
- "单步运行"和"观察变量"功能正常.

## Window

<span style="color:red">无法开启调试.</span>
