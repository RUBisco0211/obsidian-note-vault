---
title: make与cmake
created: 2025-12-26 15:25:52
updated: 2026-01-16 11:34:30
---
#### make 构建流程

1. 编写 Makefile
   make 会自动查找`makefile`或`Makefile`文件，一般用`Makefile`
2. 执行`make`或用`-f {文件路径}`指定文件

#### Makefile 编写

-   make 规则格式

```makefile
target(目标): requires(依赖) # {输出文件}:{输入文件}
    recipe(由依赖得到目标的方法) # [tab] {编译指令}
```

-   基础：指定编译指令

```makefile
# Makefile
main: main.cpp # {输出文件}:{输入文件}
    g++ -o main main.cpp # [tab] {编译指令}
```

-   指定逐步编译指令

```makefile
# Makefile
main: main.o # {输出文件}:{输入文件}
    g++ -o main main.o # [tab] {编译指令}
main.o: main.cpp
    g++ -c main.cpp
```
