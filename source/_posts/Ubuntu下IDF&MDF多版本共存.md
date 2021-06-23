---
title: Ubuntu 下 IDF&MDF 多版本共存
date: 2021-06-18
categories: 
- Espressif
tags:
- IDF
- MDF
---
# 环境

- Ubuntu 16.04
- ESP-IDF v4.2.1 , v4.3, master
- ESP-MDF master

# 配置

1. 从 Github 下载各个版本的 IDF/MDF 到 `~/esp/`目录下.
   ```powershell
   >>> ls ~/esp/
   esp-idf-master  esp-idf-v4.2.1  esp-idf-v4.3  esp-mdf
   ```

2. 通过任一版本 IDF/MDF 安装工具链, 如下通过`MDF`进行安装:
   ```powershell
   >>> cd ~/esp/esp-mdf/esp-idf
   >>> ./install.sh
   ```
3. 修改`~/.bashrc`文件, 配置各个环境切换命令.
   ```powershell
   >>> vim ~/.bashrc
   
   # 末尾增加如下几行
   alias get_idf_master='export IDF_PATH="$HOME/esp/esp-idf-master";. $HOME/esp/esp-idf-master/export.sh'
   alias get_idf_42='export IDF_PATH="$HOME/esp/esp-idf-v4.2.1";. $HOME/esp/esp-idf-v4.2.1/export.sh'
   alias get_idf_43='export IDF_PATH="$HOME/esp/esp-idf-v4.3";. $HOME/esp/esp-idf-v4.3/export.sh'
   alias get_mdf='export IDF_PATH="$HOME/esp/esp-mdf/esp-idf";. $HOME/esp/esp-mdf/export.sh'
   
   >>> source ~/.bashrc
   ```

4. 在终端调用指定的命令, 可以切换至相应的 IDF/MDF 版本环境, 于是能够使用该环境下的`idf.py`工具.
   ```powershell
   # 切换至 esp-idf-master
   >>> get_idf_master
   >>> idf.py --version
   ESP-IDF v4.4-dev-1594-g1d7068e4b-dirty
   
   # 切换至 esp-idf-v4.2.1
   >>> get_idf_42
   >>> idf.py --version
   ESP-IDF v4.2.1-dirty
   
   # 切换至 esp-idf-v4.3
   >>> get_idf_43
   >>> idf.py --version
   ESP-IDF v4.3.0
   
   # 切换至 esp-mdf
   >>> get_mdf
   >>> idf.py --version
   ESP-IDF v4.3-beta3-dirty
   ```
