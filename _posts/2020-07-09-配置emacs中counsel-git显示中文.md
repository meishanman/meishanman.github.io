# 配置emacs中counsel-git显示中文

在终端下的emacs，使用counsel-git，如果存在中文文件名，会显示乱码，推测其显示的内容是git提供的，所有尝试配置将git的输出显示中文不乱码

``` bash
$ git config --global core.quotepath false
```

会在用户目录下生成一个文件"~/.gitconfig"或在这个文件添加如下内容:
``` 
[core]
        quotepath = false
```
