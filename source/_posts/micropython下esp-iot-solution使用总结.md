---
title: micropython 下 esp-iot-solution 使用总结
date: 2021-07-01
categories: 
- Espressif
tags:
- MicroPython
- ESP-iot-solution
---
# 摘要

本文档以 **esp-iot-solution** 下的 ***componets/led/light*** 组件为例, 将它改写为 **micropython** 下 **C** 模组, 实现其通过 **python** 脚本调用函数运行, 模组源码见 [Github](https://github.com/fairytail655/micropython_iot). 

 <!--more-->

# 环境

- Ubuntu18.04
- ESP-IDF v4.3
- micropython v1.16
- ESP-iot-solution master

# 制作模组

1. 进入 micropython 下 esp32 目录, 新建 esp-iot-solution 组件的文件夹.
   ```powershell
   cd micropython/ports/esp32
   mkdir micropython_iot
   cd micropython_iot
   ```
2. 新建文件夹 `iot_light`, 复制 `micropython/examples/usercmodule/cexample` 目录下配置文件.
   ```powershell
   mkdir iot_light
   cp ../../../examples/usercmodule/cexample/micropython.cmake iot_light/
   cp ../../../examples/usercmodule/cexample/micropython.mk iot_light/
   cp ../../../examples/usercmodule/micropython.cmake .
   ```
3. 复制 `esp-iot-solution/components/led/light` 目录下 `iot_light.h` 和 `iot_light.c` 到 `iot_light` 文件夹下, 其目录结构如下:
   ```
   -- micropython_iot
         |-- iot_light
               |-- iot_light.h
               |-- iot_light.c
               |-- micropython.cmake
               |-- micropython.mk
         |-- micropython.cmake
   ```
4. 修改配置文件 `micropython_iot/iot_light/micropython.cmake`, 具体原理见[文档](https://docs.micropython.org/en/latest/develop/cmodules.html), 内容如下:
   ```makefile
   # Create an INTERFACE library for our C module.
   add_library(iot_light INTERFACE)
   
   # Add our source files to the lib
   target_sources(iot_light INTERFACE
       ${CMAKE_CURRENT_LIST_DIR}/iot_light.c
   )
   
   # Add the current directory as an include directory.
   target_include_directories(iot_light INTERFACE
       ${CMAKE_CURRENT_LIST_DIR}
   )
   
   # Link our INTERFACE library to the usermod target.
   target_link_libraries(usermod INTERFACE iot_light)
   ```
5. 修改配置文件 `micropython_iot/iot_light/micropython.mk`, 内容如下:
   ```makefile
   EXAMPLE_MOD_DIR := $(USERMOD_DIR)
   
   # Add all C files to SRC_USERMOD.
   SRC_USERMOD += $(EXAMPLE_MOD_DIR)/iot_light.c
   
   # We can add our module folder to include paths if needed
   # This is not actually needed in this example.
   CFLAGS_USERMOD += -I$(EXAMPLE_MOD_DIR)
   CEXAMPLE_MOD_DIR := $(USERMOD_DIR)
   ```
6. 修改配置文件 `micropython_iot/micropython.cmake`, 内容如下:
   ```makefile
   # This top-level micropython.cmake is responsible for listing
   # the individual modules we want to include.
   # Paths are absolute, and ${CMAKE_CURRENT_LIST_DIR} can be
   # used to prefix subdirectories.
   
   # Add the C example.
   include(${CMAKE_CURRENT_LIST_DIR}/iot_light/micropython.cmake)
   ```
7. 修改 `micropython_iot/iot_light/iot_light.c` 和 `micropython_iot/iot_light/iot_light.h`, 使其符合 **micropython C** 模组语法要求, 具体原理见[文档](https://micropython-usermod.readthedocs.io/en/latest/usermods_10.html), 源码见 [Github](https://github.com/fairytail655/micropython_iot).
8. 为了使组件能够通过 `idf.py menuconfig` 命令进行配置, 在 `micropython_iot/iot_light/` 目录下新建文件 `Kconfig.in`, 内容如下:
   ```
   config LIGHT_GPIO_NUM
       int "GPIO num of light"
       range 1 30
       default 25
   ```
   
   同时在 `micropython/ports/esp32/main` 目录下新建文件 `Kconfig.projbuild` , 内容如下:
   ```
   menu "IoT-Solution Configuration"
   
       config IOT_LIGHT_ENABLE
           default y
           bool "Enable IoT light functionality."
   
       menu "GPIO num"
           visible if IOT_LIGHT_ENABLE
   
           orsource "../micropython_iot/iot_light/Kconfig.in"
       endmenu
   
   endmenu
   ```
9. 在 `micropython_iot/iot_light` 目录下新建 python 测试用例文件 `examples.py`, 内容如下:
   ```python
   import iot_light
   import time
   
   CHANNEL_ID_R = 0
   CHANNEL_ID_G = 1
   CHANNEL_ID_B = 2
   CHANNEL_R_IO = 25
   CHANNEL_G_IO = 26
   CHANNEL_B_IO = 27
   
   LEDC_TIMER_0 = 0
   LEDC_HIGH_SPEED_MODE = 0
   LEDC_TIMER_13_BIT = 13
   LEDC_CHANNEL_0 = 0
   LEDC_CHANNEL_1 = 1
   LEDC_CHANNEL_2 = 2
   
   LIGHT_FULL_DUTY = (1 << LEDC_TIMER_13_BIT) - 1
   LIGHT_DUTY_FADE_2S = 2
   
   TAG = "light test"
   
   
   light = iot_light.create(LEDC_TIMER_0, LEDC_HIGH_SPEED_MODE, 1000, 3, LEDC_TIMER_13_BIT)
   
   iot_light.channel_regist(light, CHANNEL_ID_R, CHANNEL_R_IO, LEDC_CHANNEL_0)
   iot_light.channel_regist(light, CHANNEL_ID_G, CHANNEL_G_IO, LEDC_CHANNEL_1)
   iot_light.channel_regist(light, CHANNEL_ID_B, CHANNEL_B_IO, LEDC_CHANNEL_2)
   
   print("(%s) stage1" % TAG)
   iot_light.duty_write(light, CHANNEL_ID_R, LIGHT_FULL_DUTY, LIGHT_DUTY_FADE_2S)
   iot_light.breath_write(light, CHANNEL_ID_R, 4000)
   iot_light.breath_write(light, CHANNEL_ID_B, 8000)
   time.sleep(5)
   
   print("(%s) stage2" % TAG)
   iot_light.blink_starte(light, (1<<CHANNEL_ID_R)|(1<<CHANNEL_ID_G)|(1<<CHANNEL_ID_B), 100)
   time.sleep(5)
   iot_light.blink_stop(light)
   
   print("(%s) stage3" % TAG)
   iot_light.duty_write(light, CHANNEL_ID_R, LIGHT_FULL_DUTY, LIGHT_DUTY_FADE_2S)
   iot_light.duty_write(light, CHANNEL_ID_G, LIGHT_FULL_DUTY // 8, LIGHT_DUTY_FADE_2S)
   iot_light.duty_write(light, CHANNEL_ID_B, LIGHT_FULL_DUTY // 4, LIGHT_DUTY_FADE_2S)
   time.sleep(5)
   
   iot_light.delete(light)
   ```

   测试脚本调用了模组中改写的函数, 其运行结果与  **esp-iot-solution** 下 `components/led/light/test/light_test.c` 的运行结果一致.

# 配置        

- 在 `micropython/ports/esp32` 目录下执行命令 `idf.py menuconfig` 可以对工程进行配置.
- 进入菜单栏 `IoT-Solution Configuration`, 可以使能和配置 **IoT_light** 功能.

# 编译&烧录

进入 `micropython/ports/esp32` 目录, 使用 `make` 或 `idf.py` 均可实现固件的编译和烧录.

## make

- 本质是调用 `idf.py` 实现功能.
- 编译, 需要指定 `USER_C_MODULES` 宏为 `micropython_iot/micropython.cmake` 文件路径(最好采用绝对路径), 如:
  ```powershell
  make USER_C_MODULES=/home/user/Desktop/micropython/ports/esp32/micropython_iot/micropython.cmake
  ```
- 烧录, 执行 `make deploy`.

## idf.py

- 编译, 需要指定 `USER_C_MODULES` 宏为 `micropython_iot/micropython.cmake` 文件路径(最好采用绝对路径), 如:
  ```
  idf.py -DUSER_C_MODULES=/home/user/Desktop/micropython/ports/esp32/micropython_iot/micropython.cmake build
  ```
- 烧录, 执行 `idf.py flash`.

# 运行脚本

```powershell
ampy --port /dev/ttyUSB0 run [file_path]    # file_path 为 micropython_iot/iot_light/example.py 的路径
```

# 模组制作要点

- 将关键词 `static` 替换为 `STATIC`.
- 常用的头文件为 `#include "py/runtime.h"`
- 不被 python 调用调用的函数无需要做太大调整, 适配接口即可.
- 需要被 python 调用的函数需要针对以下几点进行调整:

  - **返回变量**
    
    变量类型需要改写为 `void` 或 `mp_obj_t`, 可以通过函数或强制转换.

  - **输入参数**
    
    参数个数在 3 个及以内时, 更改所有变量类型为 `mp_obj_t`, 可以在函数体内转换为需要的变量类型, 如:
    ```
    typedef struct {
      int a;
      int b;
    } data_t;
    
    // 原函数
    int add_int(data_t *data, int num)
    {
      return (data->a + data->b + num);
    }
    
    // 修改后
    STATIC mp_obj_t add_int(mp_obj_t data_obj, mp_obj_t num_obj)
    {
      data_t *data = (data *)data_obj;                  // 强制转换为自定义类型的指针
      int num = mp_obj_get_int(num_obj);                // 使用函数获取相应类型变量的值
      return mp_obj_new_int(data->a + data->b + num);   // 使用函数返回 mp_obj_t 类型的值
    }
    ```
    
    参数个数在 3 个以上时, 函数接口需要改写为 `(mp_uint_t n_args, const mp_obj_t *arg_in)`, 其中, `n_args` 为变量个数, `arg_in` 为指向首个变量的指针. 这种情况下也需要对调用该函数的代码进行调整, 如:
    ```
    // 原函数
    int add_int(int a, int b, int c, int d)
    {
      return (a + b + c + d);
    }
    void test(void)
    {
      add_int(0, 1, 2, 3);
    }
    
    // 修改后
    STATIC mp_obj_t add_int(mp_uint_t n_args, const mp_obj_t *arg_in)
    {
      int a = mp_obj_get_int(arg_in[0]);
      int b = mp_obj_get_int(arg_in[1]);
      int c = mp_obj_get_int(arg_in[2]);
      int d = mp_obj_get_int(arg_in[3]);
      
      return mp_obj_new_int(a + b + c + d);
    }
    void test(void)
    {
      mp_obj_t param[4];
      param[0] = mp_obj_new_int(0);
      param[1] = mp_obj_new_int(1);
      param[2] = mp_obj_new_int(2);
      param[3] = mp_obj_new_int(3);
      
      add_int(sizeof(param), param);
    }
    ```
  - **注册**
    
    每个被 python 调用的函数都需要通过 `MP_DEFINE_CONST_FUN_OBJ_*` 宏注册一个 `obj` 对象供后续 **模组定义** 使用.
    
    当函数参数在 3 个及以内时, `*` 代表参数个数, 如:
    ```c_cpp
    STATIC mp_obj_t add_int(mp_obj_t data_obj, mp_obj_t num_obj)
    {
      ...
    }
    MP_DEFINE_CONST_FUN_OBJ_2(add_int_obj, add_int);    // 生成名为 add_int_obj 的 obj 对象
    ```
    
    当函数参数在 3 个以上时, `*` 为 `_VAR`, 如:
    ```c_cpp
    STATIC mp_obj_t add_int(mp_uint_t n_args, const mp_obj_t *arg_in)
    {
      ...
    }
    MP_DEFINE_CONST_FUN_OBJ_VAR(add_int_obj, num, add_int);   // num 为实际变量个数
    ```
  - **模组定义**
    
    为了使 C 模组的函数能够被 python 调用, 需要定义 `mp_rom_map_elem_t` 类型的元素表, 配置模组的属性和方法, 并使用 `MP_DEFINE_CONST_DICT` 宏为其注册一个 `dict` 对象, 如下:
    ```c_cpp
    STATIC const mp_rom_map_elem_t module_globals_table[] = {
        { MP_ROM_QSTR(MP_QSTR___name__), MP_ROM_QSTR(MP_QSTR_iot_light) },
        { MP_ROM_QSTR(MP_QSTR_add_int), MP_ROM_PTR(&add_int_obj) },
    };
    STATIC MP_DEFINE_CONST_DICT(module_globals, module_globals_table);    // 生成名为 module_globals 的 dict 对象
    ```
    
    所有使用 `MP_DEFINE_CONST_FUN_OBJ_*` 注册的函数都需要填入元素表, 否则编译会报错.
    
    最后, 根据元素表定义 `mp_obj_module_t` 类型的模组对象, 使用 `MP_REGISTER_MODULE` 宏注册模组用于 python 调用, 如下:
    ```c_cpp
    #define CONFIG_ADD_INT_ENABLE 1
    
    const mp_obj_module_t add_int_module = {
        .base = { &mp_type_module },
        .globals = (mp_obj_dict_t *)&module_globals,
    };
    
    MP_REGISTER_MODULE(MP_QSTR_add_int, add_int_module, CONFIG_ADD_INT_ENABLE); // 可以通过 CONFIG_ADD_INT_ENABLE 控制是否使能该模组
    ```

# 参考文档

- [Extending MicroPython in C](https://docs.micropython.org/en/v1.15/develop/cmodules.html#).
- [micropython-usermod](https://micropython-usermod.readthedocs.io/en/latest/usermods_01.html).
