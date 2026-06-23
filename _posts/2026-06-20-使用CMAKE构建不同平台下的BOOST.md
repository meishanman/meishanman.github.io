# 使用CMAKE构建不同平台下的BOOST

## 需求

需要在不同的平台用到BOOST, 如果都是使用头文件库，那样最好，直接包含头文件就可以。但是有些不是头文件库，就需要编译。将BOOST编译成不同平台下的库包含静态库和动态库。

## 准备工作

- cmake
- boost, 需要下载cmake版本。在https://github.com/boostorg/boost/releases有提供。

## 步骤

编译X64平台：

```
mkdir build_x64
cd build_x64
cmake .. -DCMAKE_INSTALL_PREFIX=/home/cjj/sources/Tool/libs/x64
cmake --build .
cmake --build . --target install
```

编译RK3568平台：
配置工具链tools.cmake:
```cmake
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR aarch64)

set(tools /home/cjj/usr/rk3568_gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/)
set(CMAKE_C_COMPILER ${tools}/bin/aarch64-rockchip1031-linux-gnu-gcc)
set(CMAKE_CXX_COMPILER ${tools}/bin/aarch64-rockchip1031-linux-gnu-g++)

set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
```
编译
```
mkdir build_rk3568
cd build_rk3568
cmake .. -DCMAKE_TOOLCHAIN_FILE=`pwd`/tools.cmake -DCMAKE_INSTALL_PREFIX=/home/cjj/sources/Tool/libs/rk3568/boost_1_90_0
cmake --build .
cmake --build . --target install
```

编译RK3576平台：
配置工具链tools.cmake:
```cmake
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR aarch64)

set(tools /home/cjj/usr/rk3576-buildroot/)
set(CMAKE_C_COMPILER ${tools}/bin/aarch64-linux-gcc)
set(CMAKE_CXX_COMPILER ${tools}/bin/aarch64-linux-g++)

set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
```
编译：
```
mkdir build_rk3576
cd build_rk3576
cmake .. -DCMAKE_TOOLCHAIN_FILE=`pwd`/tools.cmake -DCMAKE_INSTALL_PREFIX=/home/cjj/sources/Tool/libs/rk3576/boost_1_90_0
cmake --build .
cmake --build . --target install
```

## 使用

```cmake
cmake_minimum_required(VERSION 3.23)
project(boost_test)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 指定boost的安装路径
set(BOOST_ROOT /home/cjj/usr/boost_1_91_0)

find_package(Boost REQUIRED COMPONENTS process)
add_executable(process_test process_test.cpp)
target_link_libraries(process_test Boost::process)
```