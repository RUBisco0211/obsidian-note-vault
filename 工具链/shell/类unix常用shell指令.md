---
title: "`ls` 即 list"
created: 2025-12-26 15:25:52
updated: 2026-01-16 22:22:32
reference: https://www.runoob.com/linux/linux-command-manual.html
---
# `ls` 即 list

`ls` 列出目录下的文件和目录

参数：

-   `-a` 列出包括隐藏目录和文件
-   `-l` 列出目录下的文件和目录的详细信息
-   `-la` 列出包括隐藏目录和文件的详细信息，缩写为`ll`

## `ls -l` 的结果

```shell
-rwxr-xr-x 1 user group size date time your_script.sh
```

这里的 `-rwxr-xr-x` 是权限字符串：

- 第一个字符：表示文件类型，`-` 代表普通文件，`d` 代表目录。
- 接下来的 `rwx`：代表文件**所有者**的权限，`r` 代表读权限，`w` 代表写权限，`x` 代表执行权限。
- 紧接着的 `r-x`：代表文件**所属组**的权限。
- 最后的 `r-x`：代表**其他用户**的权限。
- 后面的数字 `1` 代表文件链接数。
- `user` 和 `group` 代表文件所有者和所属组的名称。
- `size` 代表文件大小，单位是字节。
- `date` 和 `time` 代表文件最后修改的日期和时间。
- 最后是文件名 `your_script.sh`。

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

# `chmod` 即 change mode 修改文件权限

```shell
chmod [options] mode file_or_directory
```

`mode` 指定了权限的设置方式，可以是符号表示法或数字表示法。

符号表示法中，`u` 代表用户（所有者），`g` 代表组，`o` 代表其他用户，`a` 代表所有人（用户、组和其他用户）。权限可以用 `+` 来添加，`-` 来移除，或者 `=` 来设置确切的权限。

例：

- 为文件所有者添加执行权限：
```bash
chmod u+x /path/to/your_script.sh
```
- 移除组的写权限：
```bash
chmod g-w /path/to/your_script.sh
```
- 设置其他用户没有权限：
```bash
chmod o-rwx /path/to/your_script.sh
```
- 为所有人添加执行权限：
```bash
chmod a+x /path/to/your_script.sh
```