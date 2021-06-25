---
title: Arduino 下 esp-iot-solution 使用总结
date: 2021-06-25
categories: 
- Espressif
tags:
- Arduino
- ESP-iot-solution
---
# 摘要

本文档以 **esp-iot-solution** 下的 ***componets/led/light***  和 ***componets/button*** 组件为例, 将它们修改为 **Arduino** 库, 实现了这两个组件在 **IDE** 上的使用.

<!--more-->

# 环境

- Ubuntu 18.04
- Arduino 1.8.15
- [arduino-esp32](https://github.com/espressif/arduino-esp32) v2.0.0 ( 本文档采用的[配置方法](https://github.com/espressif/arduino-esp32/blob/master/docs/arduino-ide/debian_ubuntu.md) )
- esp-iot-solution [master](https://github.com/espressif/esp-iot-solution)

# componets/led/light

## 步骤

1. 从 esp-iot-solution 目录中复制 `componets/led/light` 到 `~/Arduino/hardware/espressif/esp32/libraries` 下, 更名为 `iot_light`.
2. 仿照 `libararies`目录下其他库目录(如 **HTTPClinet**)修改 `iot_light` 目录结构, 修改如下:
   ```
   -- iot_light                # 未用到的文件均删除
         |-- examples
         |-- src
               |-- iot_light.h
               |-- light_test.c
               |-- light.c
   ```
3. 复制其他库目录下的 `keywords.txt` 和 `library.properties` 到 `iot_light` 目录下, 可根据实际需要更改内容, 本文档修改如下:
   ```
   # keywords.txt:
   
   #######################################
   # Syntax Coloring Map For light
   #######################################
   
   #######################################
   # Library (KEYWORD3)
   #######################################
   
   iot_light    KEYWORD3        RESERVED_WORD
   
   #######################################
   # Datatypes (KEYWORD1)
   #######################################
   
   light_channel_t    KEYWORD1        DATA_TYPE
   
   light_handle_t    KEYWORD1        DATA_TYPE
   
   light_duty_mode_t    KEYWORD1        DATA_TYPE
   
   #######################################
   # Methods and Functions (KEYWORD2)
   #######################################
   
   iot_light_create            KEYWORD2
   iot_light_channel_regist    KEYWORD2
   iot_light_delete            KEYWORD2
   iot_light_duty_write        KEYWORD2
   iot_light_breath_write        KEYWORD2
   iot_light_blink_starte        KEYWORD2
   iot_light_blink_stop        KEYWORD2
   
   #######################################
   # Constants (LITERAL1)
   #######################################
   
   LIGHT_SET_DUTY_DIRECTLY    LITERAL1        RESERVED_WORD_2
   LIGHT_DUTY_FADE_1S    LITERAL1        RESERVED_WORD_2
   LIGHT_DUTY_FADE_2S    LITERAL1        RESERVED_WORD_2
   LIGHT_DUTY_FADE_3S    LITERAL1        RESERVED_WORD_2
   LIGHT_DUTY_FADE_MAX    LITERAL1        RESERVED_WORD_2
   
   ```
   ```
   # library.properties:
   
   name=iot_light
   version=1.0.0
   author=Liu
   maintainer=
   sentence=iot Light sentence
   paragraph=iot Light paragraph
   category=iot
   url=
   architectures=esp32
   ```
4. 在 `examples` 下新建 `light_test/light_test.ino` (该文件和文件夹名需保持一致, 否则 **Arduino IDE** 的 Examples 下不会显示该例程), 最终目录结构为:
   ```
   -- iot_light          
         |-- examples
               |-- light_test
                     |-- light_test.ino
         |-- src
               |-- iot_light.h
               |-- light_test.c
               |-- light.c
         |-- keywords.txt
         |-- library.properties
   ```
   
    `light_test.ino` 内容如下:
   ```
   #include <iot_light.h>
   
   void setup()
   {
     Serial.begin(115200);
     Serial.println("Setup done");
   
     light_handle_t led0 = iot_light_create(LEDC_TIMER_0, LEDC_HIGH_SPEED_MODE, 5000, 1, LEDC_TIMER_13_BIT);
     ESP_ERROR_CHECK(iot_light_channel_regist(led0, 0, GPIO_NUM_25, LEDC_CHANNEL_0));
     ESP_ERROR_CHECK(iot_light_duty_write(led0, 0, 1<<5, LIGHT_SET_DUTY_DIRECTLY));
     iot_light_breath_write(led0, 0, 5000);
   }
   
   void loop()
   {
     Serial.println("Hello world");
     delay(1000);
   }
   ```

## 效果

- 重启 Arduino IDE.
- 点击 `File` -> `Examples` -> `iot_light` -> `light_test`, 这时会弹出上节第 4 步预设的例程, 可以进行编译运行.
-  点击 `Sketch` -> `Include Library` -> `iot_light`, 这时会在文件中插入 `#include <iot_light.h>`.

# componets/button

## 步骤

1. 同 **componets/led/light** 步骤 1-3, 复制例程并修改目录结构, 本文档修改结果如下:
   ```
   -- iot_button        
         |-- examples
         |-- src
               |-- button_adc.c
               |-- button_adc.h
               |-- button_gpio.c
               |-- button_gpio.h
               |-- button_test.c
               |-- iot_button.c
               |-- iot_button.h
         |-- keywords.txt                      # 自行修改, 不在此展示
         |-- library.properties                # 自行修改, 不在此展示
   ```
2. 由于原组件采用 `Kconfig` 文件来配置一些宏定义, 而 Arduino IDE 下无法使用 `menuconfig`, 所以要手动创建头文件来定义组件中需要用到的宏. 
3. 本文档在 `src` 目录下新建 `button_config.h` 文件, 根据原组件的 `Kconfig` 文件修改, 内容如下:
   ```
   #ifndef IOT_BUTTON_CONFIG_H
   #define IOT_BUTTON_CONFIG_H
   
   #define CONFIG_BUTTON_PERIOD_TIME_MS                5           # 均采用默认参数
   #define CONFIG_BUTTON_DEBOUNCE_TICKS                2
   #define CONFIG_BUTTON_SHORT_PRESS_TIME_MS            180
   #define CONFIG_BUTTON_LONG_PRESS_TIME_MS            1500
   #define CONFIG_ADC_BUTTON_MAX_CHANNEL                3
   #define CONFIG_ADC_BUTTON_MAX_BUTTON_PER_CHANNEL    8
   #define CONFIG_ADC_BUTTON_SAMPLE_TIMES                1
   
   #endif
   ```
   
   最终目录结构如下:
   ```
   -- iot_button       
         |-- examples
         |-- src
               |-- button_adc.c
               |-- button_adc.h
               |-- button_config.h
               |-- button_gpio.c
               |-- button_gpio.h
               |-- button_test.c
               |-- iot_button.c
               |-- iot_button.h
         |-- keywords.txt                    
         |-- library.properties                
   ```

4. 在以下文件的头部添加头文件 `#include "button_config.h"`.
   ```
   button_adc.c, button_gpio.c, button_test.c, iot_button.c
   ```

5. 本文档没有对该组件编写 Arduino 例程.

## 效果

- 重启 Arduino IDE.
- 点击 `Sketch` -> `Include Library` -> `iot_button`, 这时会在文件中插入以下头文件:
  ```
  #include <button_config.h>
  #include <button_gpio.h>
  #include <button_adc.h>
  #include <iot_button.h>
  ```
