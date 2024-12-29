# ROS 程序编写之 Hello ROS

我们的代码会被组织到软件包中，而我们自己编写的软件包需要包含在工作区

## 创建工作区
``` bash
$ cd ~
$ mkdir ros # 创建工作区目录
$ cd ros
$ mkdir src	# 创建源代码目录
```
## 创建功能包
``` bash
$ cd src
$ catkin_create_pkg agitr # 创建的包名首字符必须是小写字母
$ ls agitr # 查看生成的文件
CMakeLists.txt  package.xml
```
	
文件说明：

- `package.xml` 包的管理文件
- `CMakeLists.txt` 代码管理文件

## 编写代码

在程序包中编写代码
``` bash
$ cd agitr
$ touch hello.cpp
```
	
hello.cpp 内容如下：
``` c++
//包含ROS的cpp库的头文件
#include <ros/ros.h>

int main(int argc, char** argv){
	//初始化ros库，第三个参数为程序启动时的默认节点名字
	ros::init(argc, argv, "hello_ros");
	
	//ros 程序的句柄，用于同ros交互，会向节点管理注册节点，如果创建多个实例，也只会注册一次
	ros::NodeHandle nh;
	
	//打印日志信息
	ROS_INFO_STREAM("Hello, ROS!");
}
```
	
## 编译程序

1.声明依赖库

将CMakeLists.txt文件中的
``` cmake
find_package(catkin REQUIRED)
```
修改为：
``` cmake
find_package(catkin REQUIRED COMPONENTS roscpp)
```
在package.xml文件中添加：
``` xml
<build_depend>roscpp</build_depend>
<run_depend>roscpp</run_depend>
```
这个文件中声明的依赖，并不会在本机编译中用到，但是发布之后会有用到这个文件。

2.声明可执行文件

去除CMakeLists.txt文件中add_executable和target_link_libraries的注释，修改为：
``` cmake
add_executable(hello hello.cpp)
target_link_libraries(hello ${catkin_LIBRARIES})
```
hello 表示生成的可执行文件，hello.cpp表示需要编译的源码，${catkin_LIBRARIES}表示上面find_package中的依赖库。

3.编译工作区
``` bash
$ cd ~/ros
$ catkin_make
$ ls
build  devel  src
```
build 和 devel 是生成的两个目录

4.设置工作区的环境
``` bash
$ source devel/setup.bash
```
这个命令只需要执行一次，添加新的软件包也不需要执行，除非目录结构发生变化。

开启新的终端进入工作区时也需要执行该命令设置环境。


## 运行程序
``` bash
$ rosrun agitr hello 
[ INFO] [1458050500.876920851]: Hello, ROS!
```
执行之前需要先启动节点管理器

## 参考

- [机器人操作系统（ROS）浅析](http://books.exbot.net/gentleros) .


