# windows_terminal下配置wls

### 需求：
- 启动windows terminal后直接进入在wls中运行的linux的home目录。

### 分析

这个需求可以分为两步：
- 配置默认终端：即windows terminal默认进入的terminal。
- 配置终端启动目录：即terminal启动后默认进入的目录。

### 配置启动目录
每一个terminal的配置格式如下：
``` 
{
    "guid": ""
    "hidden": false,
    "name": "Ubuntu-20.04",
    "source": "Windows.Terminal.Wsl",
    "startingDirectory": "//wsl$/Ubuntu-20.04/home/cjj" 
},
```

配置项“startingDirectory”用于配置系统启动的初始目录

### 配置默认终端

将顶层的配置项"defaultProfile"，配置为对应的terminal的guid。启动windows terminal就会进入到对应的terminal。
