---
title: micropython 开发 ESP32
date: 2021-07-01
categories: 
- Espressif
tags:
- MicroPython
---
# 环境

- Ubuntu18.04
- ESP-IDF v4.3
- micropython v1.16

<!--more-->

# 步骤

1. 获取 **micropython** 源码.
   ```powershell
   git clone https://github.com/micropython/micropython.git
   ```
2. 配置 **ESP-IDF** 环境变量, 见[文档](https://fairytail655.github.io/2021/06/18/Ubuntu%E4%B8%8BIDF&MDF%E5%A4%9A%E7%89%88%E6%9C%AC%E5%85%B1%E5%AD%98/).
   ```powershell
   get_idf_43
   ```
3. 编译工具.
   ```powershell
   make -C mpy-cross
   ```
4. 进入 *ports/esp32* 目录, 该目录本质上为一个基于 ESP-IDF 的工程.
   ```powershell
   cd ports/esp32
   make submodules
   ```
5. 编译工程, 以下任一命令均可.
   ```powershell
   make            # 执行完会生成 build-GENERIC 文件夹
   ```
   ```powershell
   idf.py build    # 执行完会生成 build 文件夹
   ```

6. 烧录.
   ```powershell
   make deploy     # 使用 make 编译则执行此命令
   ```
   ```powershell
   idf.py flash    # 使用 idf.py 编译则执行此命令
   ```

# 参考文档

- [MicroPython port to the ESP32](https://github.com/micropython/micropython/blob/master/ports/esp32/README.md)