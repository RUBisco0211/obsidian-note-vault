---
title: "`ls` 即 list"
created: 2025-12-26 15:25:52
updated: 2026-01-16 21:35:44
---
# `ls` 即 list

`ls` 列出目录下的文件和目录

参数：

-   `-a` 列出包括隐藏目录和文件

-   `-l` 列出目录下的文件和目录的详细信息
    权限、用户名、用户组名、大小、修改时间、文件或目录名称, total 为所有文件数量

```bash
rubisco0211@LAPTOP-OFVDFEMH:~/code/noob_json$ ls -l
total 24
-rw-r--r-- 1 rubisco0211 rubisco0211  655 Feb  2 17:32 CMakeLists.txt
drwxr-xr-x 3 rubisco0211 rubisco0211 4096 Feb  3 15:40 build
drwxr-xr-x 2 rubisco0211 rubisco0211 4096 Feb  2 11:52 include
-rw-r--r-- 1 rubisco0211 rubisco0211  723 Feb  3 15:41 run.cpp
drwxr-xr-x 2 rubisco0211 rubisco0211 4096 Feb  2 11:34 src
drwxr-xr-x 2 rubisco0211 rubisco0211 4096 Jan 30 22:09 test
```

-   `-la` 列出包括隐藏目录和文件的详细信息，缩写为`ll`

# `cd` 即 change directory

`cd {相对或绝对路径}` 切换到某个目录

`cd -` 回到上一个目录

# `pwd` 即 print working directory

`pwd` 显示当前目录

# `cat` 即 concatenate 连接文本文件

`cat {file1} {file2} ...` 连接文本文件并打印在控制台

`cat {file1} {file2} ... > {output_file}` 把文本文件 file1、2...连接并写入 output_file

# `head` 显示文本文件头部内容；`tail` 显示文本文件尾部内容

参数：

-   `--lines={行数}`

# `less` 显示文本文件内容

快捷键：

-   按`Q`退出

# `file` 查看文件信息

# `which` 查看可执行文件路径

```bash
rubisco0211@LAPTOP-OFVDFEMH:~/code/noob_json$ which gcc
/usr/bin/gcc
```

# `echo` 打印

# `chmod` 即change mode 修改文件权限

使用 `ls -l`查看目录下文件详细信息

```shell
rubisco0211@LAPTOP-OFVDFEMH:~/code/noob_json$ ls -l
total 24
-rw-r--r-- 1 rubisco0211 rubisco0211  655 Feb  2 17:32 CMakeLists.txt
drwxr-xr-x 3 rubisco0211 rubisco0211 4096 Feb  3 15:40 build
drwxr-xr-x 2 rubisco0211 rubisco0211 4096 Feb  2 11:52 include
-rw-r--r-- 1 rubisco0211 rubisco0211  723 Feb  3 15:41 run.cpp
drwxr-xr-x 2 rubisco0211 rubisco0211 4096 Feb  2 11:34 src
drwxr-xr-x 2 rubisco0211 rubisco0211 4096 Jan 30 22:09 test
```

这里的 `-rw-r--r--` 就是权限字符串：

- 第一个字符：表示文件类型，`-` 代表普通文件，`d` 代表目录。
- 接下来的 `rwx`：代表文件所有者的权限，`r` 代表读权限，`w` 代表写权限，`x` 代表执行权限。
- 紧接着的 `r-x`：代表文件所属组的权限。
- 最后的 `r-x`：代表其他用户的权限。
- 后面的数字 `1` 代表文件链接数。
- `user` 和 `group` 代表文件所有者和所属组的名称。
- `size` 代表文件大小，单位是字节。
- `date` 和 `time` 代表文件最后修改的日期和时间。
- 最后是文件名 `your_script.sh`。