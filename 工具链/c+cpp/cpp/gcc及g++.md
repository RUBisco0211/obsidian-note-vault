---
title: gcc及g++
created: 2025-12-26 15:25:52
updated: 2026-01-16 11:35:03
---
#### 源文件到可执行文件的过程

1. 预处理 Preprocessing `.c -> .i`
   处理预编译，替换宏，删除注释等
   `gcc -E main.c -o main.i`
2. 编译 Compilation `.i -> .s`
   词法分析、语法分析、语义分析和优化，生成汇编指令
   `gcc -S main.i`
3. 汇编 Assembly `.s -> .o`
   把汇编指令翻译成二进制机器指令
   `gcc -c main.s`
4. 链接 Linking `.o -> 可执行文件.out或.exe或无扩展名`
   对二进制文件写入运行需要的依赖信息
   `gcc -o main main.o`

##### 参数和选项：

-   `-o {输出文件}` 指定输出文件
-   `-E` 进行到预处理阶段 `gcc -E main.c -o main.i`
-   `-S` 进行到编译阶段 `gcc -S main.i`
-   `-c` 进行到汇编阶段 `gcc -c main.s`
-   `-l {库名称}`
-   `-L` 指定库文件路径
-   `-I` 指定头文件路径
-   `-f input-charset={源文件编码格式}` 指定源文件编码格式
-   `-f exec-charset={运行环境编码格式}` 指定运行环境编码格式
