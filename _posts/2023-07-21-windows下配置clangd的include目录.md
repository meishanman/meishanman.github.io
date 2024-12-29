# windows下配置clangd的include目录

windows 的配置，配置路径:C:\Users\chenj\AppData\Local\clangd\config.yaml

配置内容：
```
CompileFlags:
Add: [
  "--include-directory=D:\\05_softwares\\portables\\c++\\include", # for Windows
] # ignore some specific warnings
```


