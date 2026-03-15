# 在MinGW下编译boost源码

## 需求

想用boost在windows下写一些小工具，CLion又自带了MinGW编译器。所以有了使用MinGW编译boost的需求。

## 准备工作

下载MinGW（x86_64-15.2.0-release-posix-seh-ucrt-rt_v13-rev1.7z），解压，将MinGW的gcc目录加入到系统目录。
- `x86_64`：表示工具本身是64位程序
- `release`：表示工具本身是release
- `posix`: 表示包含posix api
- `seh`: 表示异常模型，用于64位系统
- `ucrt`: 表示win10以上的运行时

下载源码(https://archives.boost.io/release/1.90.0/source/boost_1_90_0.7z)。

## 编译

解压boost源码，通过cmd进入到解压目录。

编译编译工具b2.
```
.\bootstrap.bat gcc
```

编译&安装：
```
b2 -j4 --build-dir=.\tmp --prefix=D:\05_softwares\portables\boost_1_90_0 --layout=system address-model=64 architecture=x86 variant=debug link=static threading=multi runtime-link=shared install
```

编译参数说明：
- `-j4`: 使用四个核心编译
- `--build-dir=.\tmp`: 编译中间文件存放的目录
- `--prefix=D:\05_softwares\portables\boost_1_90_0`: 安装目录
- `--layout=system`: 库的命令方式libboost_xxxx.a
- `address-model=64 architecture=x86`: 编译成64位库
- `variant=debug`: 编译成debug库，方便调试，发布时通过strip减小体积
- `link=static`: 编译成静态库
- `threading=multi `: 启用多线程支持
- `runtime-link=shared`: 动态态链接c++运行时
- `install`: 安装

编译完成后在目标目录会存在三个目录
- `include`: 头文件目录
- `lib`: 库文件目录
- `shared`: 其他文件

## 测试

json_test.cpp 文件内容：
```cpp
#include <string>
#include <iostream>

#include "boost/json.hpp"

int main(int argc, char* argv[]){
    boost::json::value jv = { 1, 2, 3 };
    std::string s = boost::json::serialize( jv );
    std::cout << s << std::endl;
    return 0;
}
```

CMakeLists.txt 文件内容：
```cmake
cmake_minimum_required(VERSION 3.23)
project(json_test)

set(CMAKE_CXX_STANDARD 17)

# 指定boost的安装路径
set(BOOST_ROOT D:\\05_softwares\\portables\\boost_1_90_0)
# 明确指定使用静态Boost库
set(Boost_USE_STATIC_LIBS ON)

find_package(Boost REQUIRED COMPONENTS json)
add_executable(json_test json_test.cpp)
target_link_libraries(json_test PRIVATE Boost::json)
# 静态链接可执行程序的运行环境
target_link_options(json_test PRIVATE -static-libgcc -static-libstdc++ -static)
```

将生成的`json_test.exe`文件放到其他没有安装MinGW的电脑上正常运行