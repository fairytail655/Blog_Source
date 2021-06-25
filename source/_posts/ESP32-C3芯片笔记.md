---
title: ESP32-C3 芯片笔记
date: 2021-06-16
categories: 
- Espressif
tags:
- ESP32-C3
---
# 整体框架

![截图](ESP32-C3芯片笔记/978a1540d369846bb4b4a181b88c6b50.png)

<!--more-->

# 芯片命名

![截图](ESP32-C3芯片笔记/b0d34752a57f336ca1b15db23c427424.png)

# Boot

- 共有 3 个 Strapping 管脚: **GPIO2**, **GPIO8**, **GPIO9**.
- 启动模式:
  
  ![截图](ESP32-C3芯片笔记/917668e93169258d05c5bd3f94f73ad1.png)
  
  ![截图](ESP32-C3芯片笔记/c10dfc5e257d1737181e3764f854e573.png)

- **SPI Boot**:
  - 常规 flash 启动方式: 支持安全启动, 程序运行在 RAM 中.
  - 直接启动方式: 不支持安全启动, 程序直接运行在 flash 中. 
    
    如需使能这一启动方式, 确保下载至 flash 的 bin 文件其前两个字(地址:0x42000000)为 0xaebd041d

# CPU 地址分布

![截图](ESP32-C3芯片笔记/4e7920e83796b3f257d07d62dd65a7f8.png)

- IRAM、DRAM、DM 地址范围以外的地址空间通过 AHB 总线访问.

# 中断

- 中断向量:
  
  ![截图](ESP32-C3芯片笔记/c5f07c2d2c818a27c60b42ea5ca58131.png)

- 15 个优先级级别, 可以分配给不同的中断.

# GDMA

![截图](ESP32-C3芯片笔记/87e7affecfb61627c242a1f54f5a9ac6.png)

# 存储器

- 地址映射:
  
  ![截图](ESP32-C3芯片笔记/c0d870276974ec0239de83d92450b1cc.png)
  
  ![截图](ESP32-C3芯片笔记/8066fb794c42e9175bfc9558886fe747.png)
  
  ![截图](ESP32-C3芯片笔记/8c0f5bdc176f848c144897765912ce6a.png)
  
  ![截图](ESP32-C3芯片笔记/ce45381ba44c63048efaef761e54f17e.png)

# 复位

- 复位等级
  
  ![截图](ESP32-C3芯片笔记/d98cf4e14d291e6bab8d9db2697b756d.png)

# 时钟

- 时钟树:
  
  ![截图](ESP32-C3芯片笔记/7f310d7afdfc39e121ca2ac15ea617c3.png)
