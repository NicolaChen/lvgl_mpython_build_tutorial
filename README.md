# ESP32上使用lv-micropython构建图形交互界面

## 1. 背景

在发现LVGL可以很好的构建小尺寸屏幕图形交互界面后，放弃了原先采购串口屏的计划，转而准备使用LVGL研发的SquareLine软件（商用版本）进行开发。

## 2. 实现路径

### 2.1 Ubuntu

1. 获取lv-micropython仓库，并更新子模块

   ```shell
   git clone https://github.com/lvgl/lv_micropython.git
   cd lv_micropython
   git submodule update --init --recursive lib/lv_bindings
   ```

2. [安装ESP-IDF并搭建环境](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/get-started/index.html#get-started-how-to-get-esp-idf)

   1. 安装所需软件包

      ```shell
      sudo apt-get install git wget flex bison gperf python3 python3-pip python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0
      ```

   2. 获取ESP-IDF

      此条分为两种方式，一为VSCode官方插件安装，二为手动安装。

      1. VSCode插件安装

         VSCode扩展中安装`Espressif IDF`，安装完成后,单击左侧出现的`ESP-IDF Explorer`图标。

         新窗口将自动跳转至`ESP-IDF Setup`，选择`EXPRESS`设置选项，可快速全面安装。ESP-IDF版本选择，如需使用ESP32-S3，须选择v4.4及以上版本。本流程编写时使用v4.1.1(release version)。

         单击右下角`Install`等待安装完成即可。

      2. 手动安装

         待续

   3. 编译并部署固件(可参考<https://www.cnblogs.com/Wind-stormger/p/15557579.html>)

      1. 编译mpy-cross

         在lv_micorpython文件夹目录下，终端运行：

         ```shell
         make -C mpy-cross
         ```

         等待完成即可，期间可进行下一步骤。

      2. 自定义ESP32模板（非必要）

         打开目录`lv_micorpython/ports/esp32/boards`，针对合适的开发板文件夹，直接编辑或编辑其副本，如NodeMCU-32S(ESP32s)使用`GENERIC`，ESP32-S3_DevKitC-1使用`GENERIC_S3`。

      3. 自定义编译配置（非必要）

         在`lv_micopython/ports/esp32`目录下，以编辑文件`Makefile`，以实现编译部署配置自定义。以下为示例：

         ```
         BOARD ?= ESP32-S3
         PORT ?= /dev/ttyUSB0
         BAUD ?= 115200
         ```

         此步骤非必要，可在后续步骤4中命令行中添加命令项实现同样效果。

      4. 针对触摸屏调整库文件参数

      5. 编译并部署ESP32所用固件


#### 2.2 MacOs

1. 获取lv-micropython仓库，并更新子模块

   ```shell
   git clone https://github.com/lvgl/lv_micropython.git
   cd lv_micropython
   git submodule update --init --recursive lib/lv_bindings
   ```

2. 获取ESP-IDF

   此条与Ubuntu下安装流程略有不同，需注意。

   1. 获取esp-idf仓库

      ```shell
      git clone -b v4.4.1 --recursive https://github.com/espressif/esp-idf.git
      ```

   2. 执行脚本，安装工具链

      ```shell
      cd esp-idf
      ./install.sh
      source export.sh
      ```

      > 执行第二步`./install.sh`时，如有如下报错：
      >
      > ```
      > WARNING: Download failure <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:997)>
      > ```
      >
      > 编辑文件`esp-idf/tools/idf_tools.py`，添加代码：
      >
      > ```python
      > import ssl
      > ssl._create_default_https_context = ssl._create_unverified_context
      > ```
      >
      > 保存后再执行`./install.sh`即可。

      > 执行第三部时，如遇到python相关报错，原因为需要使用python3进行编译，尝试将python3软链接至python，或修改`esp-idf/export.sh`文件中L97~L99：
      >
      > ```shell
      > __verbose "Using Python interpreter in $(which python3)"
      > __verbose "Checking if Python packages are up to date..."
      > python3 "${IDF_PATH}/tools/check_python_dependencies.py" || return 1
      > ```
   
   3. 构建固件并部署烧录
   
      

## 3. 关于TFT-LCD触摸显示屏选择

### 3.1 串口屏

目前未看到4.0''以上的带针脚的串口屏

### 3.2 并口屏

- 刷新率问题

  普遍不高，20帧上下
