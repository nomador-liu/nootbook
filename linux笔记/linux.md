# linux

## 入门指令

### date

显示当前日期星期具体时间时区

### clear

清除当前终端中所有的内容

### history

`history`  查看之前的操作信息与编号

`history -c`  清空历史操作信息

### !命令

1.  历史命令引用

   ​	**`!!`**  重复执行上一条命令。

   ​	**`!n`**  执行历史命令列表中第 `n` 条命令（`n` 是历史编号）。

   ​	**`!string`**：执行最近一条以 `string` 开头的命令。

   ​	**`!?string?`**：执行最近一条包含 `string` 的命令。

2.  历史命令替换

   ​	**`^old^new`**：执行上一条命令，但将 `old` 替换为 `new`。

   ```bash
   $ cp file1.txt /tmp/error/  # 假设目录写错了
   $ ^error^correct  # 等同于 "cp file1.txt /tmp/correct/"
   ```

   ​	**`!$`**：引用上一条命令的最后一个参数。

   ```bash
   $ mkdir mydir
   $ cd !$  		# 等同于 "cd mydir"
   ```

   ​	**`!:\*`**：引用上一条命令的所有参数（不包括命令本身）。

   ```bash
   $ mv file1.txt file2.txt dir/
   $ cp !:* newdir/  # 等同于 "cp file1.txt file2.txt dir/ newdir/"
   ```

3.  否定操作符

   ​	在某些命令中，`!` 表示取反或否定的含义。

   ​	**`grep -v` 或 `! grep`**：过滤掉包含特定内容的行。

   ```bash
   $ cat file.txt | grep -v "error"  # 显示不包含 "error" 的行
   $ cat file.txt | ! grep "error"   # 同上
   ```

   ​	**`test` 命令中的否定**：

   ```python
   $ if [ ! -f file.txt ]; then echo "文件不存在"; fi
   ```

   

4. 在脚本中禁用历史扩展

   ​	如果你不希望脚本中的 `!` 被解释为历史扩展，可以使用 `set +H` 禁用：

   ```python
   #!/bin/bash
   set +H  # 禁用历史扩展	
   echo "! 现在只是普通字符"	
   ```

   

### echo

`echo`，其主要功能是将文本内容输出到标准输出（通常指终端屏幕）。

**`-n`**：输出文本后不换行。

```bash
echo -n "Hello "
echo "World"
# 输出：Hello World（在同一行）
```

**`-e`**：启用对转义字符的解释，常见的转义字符如下：

- **`\n`**：换行。

- **`\t`**：制表符（Tab 键）。

- **`\\`**：反斜杠本身。

- **`\"`**：双引号。

- **`\033[格式m`**：ANSI 转义序列，用于设置文本格式（如颜色、加粗等）。

  ```bash
  echo -e "Line 1\nLine 2"
  # 输出：
  # Line 1
  # Line 2
  
  echo -e "Column1\tColumn2\tColumn3"
  # 输出：Column1    Column2    Column3
  ```

  **注意**：`\033[0m`的作用是恢复默认格式，避免后续文本也受影响。

输出变量值

借助`echo`可打印变量的值，变量前需加`$`符号。

```bash
name="Alice"
echo "My name is $name"
# 输出：My name is Alice
```

颜色与格式设置（使用 ANSI 转义序列）
	格式：\033[属性;前景色;背景色m
	前景色（30-37）：黑、红、绿、黄、蓝、紫、青、白。
	背景色（40-47）：黑、红、绿、黄、蓝、紫、青、白。
	属性：
		0：默认。
		1：加粗。
		4：下划线。
		7：反显（前景色与背景色互换）。

```bash
# 红色文本
echo -e "\033[31mThis is red text\033[0m"

# 绿色加粗文本
echo -e "\033[1;32mThis is bold green text\033[0m"

# 黄色背景上的蓝色文本
echo -e "\033[34;43mThis is blue text on yellow background\033[0m"
```

将输出重定向到文件

```bash
echo "Hello" > test.txt  # 创建或覆盖test.txt，内容为"Hello"
echo "World" >> test.txt  # 在test.txt末尾添加"World"
```

打印特殊字符

**双引号**：会对变量和转义字符进行解析。

```bash
var=123
echo "$var"  # 输出：123
echo "$(date)"  # 输出当前日期时间
```

**单引号**：会将文本原样输出，变量和命令不会被解析

```bash
var=123
echo '$var'  # 输出：$var
echo '$(date)'  # 输出：$(date)
```

### man

man命令是一个极为重要的工具，它的主要作用是显示系统手册页，这些手册页包含了各种命令、系统调用、库函数等的详细文档。

**`-f`**（等同于`whatis`）：显示命令的简短描述。

```bash
man -f ls
# 输出：ls (1) - list directory contents
```

**`-k`**（等同于`apropos`）：通过关键字搜索相关手册页

```bash
man -k file
# 输出所有包含"file"的手册页条目
```

`-l`：显示本地手册页文件（用于查看自定义手册）。

```bash
man -l ./myapp.1
```

**`-a`**：依次显示所有章节中匹配的手册页。

```bash
man -a passwd  # 显示用户命令(1)和文件格式(5)的passwd手册
```

**`-w`**：显示手册页文件的位置。

```bash
man -w ls
# 输出：/usr/share/man/man1/ls.1.gz
```



### whereis

只能查找二进制文件、手册页和源代码，无法查找普通文件（如文档、日志等）。

- `-b`：仅查找二进制文件（如 `/bin/ls`）。
- `-m`：仅查找手册页文件（如 `/usr/share/man/man1/ls.1.gz`）。
- `-s`：仅查找源代码文件（如 `.c` 文件）。
- `-u`：查找上述类型以外的其他文件（例如配置文件）。

```bash
$ whereis ls
ls: /bin/ls /usr/share/man/man1/ls.1.gz

$ whereis linux
linux: /usr/src/linux /usr/share/man/man1/linux.1.gz

# 查看当前内核版本
$ uname -r
5.15.0-76-generic

# 查找内核镜像文件
$ whereis vmlinuz
vmlinuz: /boot/vmlinuz-5.15.0-76-generic

# 查找内核配置文件
$ whereis config
config: /boot/config-5.15.0-76-generic
```



## 基础知识

### 命令行提示符#和$

在 Linux 和 Unix 系统的命令行中，`#` 和 `$` 是两种常见的**命令行提示符**（Prompt），用于区分用户权限级别。

### &&命令符

&& 是一个逻辑运算符，称为逻辑与（Logical AND）。它的作用是将多个命令连接在一起，只有当前一个命令成功执行（返回状态码 0）时，才会执行后一个命令。





## 基础文件与目录管理

### ls

| 选项      | 功能描述                                                     |
| --------- | ------------------------------------------------------------ |
| ==`-a`==  | ==显示所有文件和目录，包括以 `.` 开头的隐藏文件。==          |
| ==`-l`==  | ==以长格式显示文件和目录的详细信息（如权限、所有者、大小、修改时间等）。== |
| ==`-h`==  | ==与 `-l` 结合使用，以人类可读的格式显示文件大小（如 KB、MB、GB）。== |
| `-t`      | 按修改时间排序，最新的文件排在最前。                         |
| `-r`      | 反转排序顺序（默认为升序，使用 `-r` 后变为降序）。           |
| `-R`      | 递归显示子目录中的内容。                                     |
| `-d`      | 仅显示目录本身，而非目录中的内容。                           |
| `--color` | 启用颜色区分不同类型的文件（如目录为蓝色，可执行文件为绿色）。 |





### cd

切换路径，后面可以传当前路径下的文件夹，也可以输入完整路径

```bash
cd / 		# 前往根目录
cd ~ 		# 前往用户目录
cd . 		# 前往当前目录，即不变
cd .. 		# 前往上一层目录
cd - 		# 前往上一次的路径
```



### pwd

显示当前所在目录的路径，从根目录开始



### mkdir

创建文件夹

```bash
$mkdir adic   						# 在当前文件目录下创建一个adic的文件夹
$mkdir -p ~/Desktop/aaa/aa/a   		# 在~/Desktop/aaa/aa/a目录下创建，此时如果目录中的层级不存在，会自动创建

```

### rmdir

删除空文件夹

```bash
$rmdir adic							# 删除当前目录下的adic文件夹，文件夹中必须为空
$rmdir -p aaa/aa/a					# 删除 a ，aa ，aaa 文件夹，所有文件夹必须问空，

```



### rm

删除文件或目录

```bash
# rm 文件名		# 删除指定文件，对于文件夹不能删除
rm -d			  # 删除空目录
rm -r			  # 递归删除目录及其内容
rm -f			  # 强制删除文件
rm -rf			  # 递归强制删除			  
```



### cp

复制文件或目录

```bash
cp /path/to/source/file.txt .  									# 最后的 . 表示当前目录
cp /path/to/source/file.txt /path/to/destination/new_name.txt 	# 复制文件并重命名
cp -r /path/to/source/dir /path/to/destination/  		 		# 递归复制目录
```



### mv

移动或重命名文件

```bash
mv file.txt /path/to/destination/  # 将 file.txt 移动到目标目录
mv file1.txt file2.txt /path/to/destination/  # 同时移动多个文件
mv dir1 /path/to/destination/  # 移动 dir1 目录到目标目录（无需 -r 选项，与 cp 不同）

mv /path/to/source/file.txt /path/to/dest/new_file.txt  # 移动并改名
mv old_dir new_dir  # 将目录从 old_dir 改名为 new_dir
mv oldname.txt newname.txt  # 将文件从 oldname.txt 改名为 newname.txt
```



### touch

创建空文件

```bash
touch 文件名 # 在当前目录创建文件
touch 路径/文件名  # 在指定的路径下创建文件

```



### find

```bash
find 目录 -type f/d  # f文件，d文件夹
find 目录 -size +/- n c/b/w/k/M/G  # 在目标路径下的所有+->大于(-->小于) n (c-字节/b-512字节的块/w-字/k-KB/M-MB/G-GB)
find 目录 -name "*.jpg"  # 目标目录下所有以.jpg结尾的文件
find 目录 -user n # 匹配用户n的文件和目录，n为用户名或者该组的id号
find 目录 -ctime +/- n # 匹配n天内被修改的文件或目录 +表示n天内，-表示n天外
find 目录 -cmin +/- n   # 匹配n分钟内被修改的文件或目录 +表示n分钟内，-表示n分钟外
find 目录 -cnewer n   	# 匹配比文件n修改时间更晚的文件
find 目录 -perm m 
```

```bash
find ~ -perm 0644  # 查找权限为 rw-r--r-- 的文件
find ~ -perm 0755  # 查找权限为 rwxr-xr-x 的目录或文件

# 包含匹配
find ~ -perm -0777  # 查找任何有执行权限的文件（rwx 中的任意一位）
find ~ -perm -u+x   # 查找用户有执行权限的文件
find ~ -perm -g+w   # 查找组有写权限的文件

#  至少一位匹配
find ~ -perm /u=x   # 查找用户有任何 x 权限的文件（用户执行权限）
find ~ -perm /g=w   # 查找组有任何 w 权限的文件（组写权限）
```

```bash
# 操作符
# -and 逻辑与 	-a
# -or 逻辑或 	-o
# -not 逻辑非 	!
# () 表示优先级优先执行内侧的()，括号要有\来将其转义
find ~ \( -type f -a -name "*.sh" \) -or \( -type d -a -name "scripts" \)
# 逻辑或：（是.sh文件） 或 （是scripts目录）
```

### du

查看目录或文件大小

```bash
du -h  # 以人类可读的格式显示大小（如 KB、MB、GB）。
du -c  # 显示所有指定文件或目录的总大小（在结果末尾追加一行总计）。
du -s  # 只显示总计，不递归显示子目录的大小（常用 -sh 组合查看总大小）。
```



### df

查看磁盘空间使用情况

| `-h`      | 以**人类可读的格式**显示大小（如 `KB`、`MB`、`GB`）。        |
| --------- | ------------------------------------------------------------ |
| `-T`      | 显示**文件系统类型**（如 `ext4`、`xfs`、`tmpfs`）。          |
| `-i`      | 显示**inode 使用情况**（而非磁盘块），用于排查 inode 耗尽问题。 |
| `-a`      | 显示**所有文件系统**，包括虚拟文件系统（如 `proc`、`sysfs`）。 |
| `-t 类型` | 只显示**指定类型的文件系统**（如 `-t ext4`）。               |
| `-x 类型` | **排除指定类型的文件系统**（如 `-x tmpfs`）。                |

| `df` | 显示**文件系统的总体使用情况**（从分区层面）。      | 查看磁盘整体容量和剩余空间。 |
| ---- | --------------------------------------------------- | ---------------------------- |
| `du` | 统计**文件 / 目录实际占用的磁盘空间**（递归计算）。 | 查找大文件、分析目录结构。   |



`df` 是监控系统磁盘空间的基础工具，通过 `-h`、`-T`、`-i` 等选项可灵活查看不同维度的存储信息。定期使用 `df` 检查磁盘使用情况，特别是关键分区（如 `/`、`/var`），可以预防因空间不足导致的系统故障。



### 相对路径与绝对路径

相对：从当前路径

绝对：从根目录出发

### tar命令

归档：tar命令不会变动源文件

| 模式 | 功能描述                                                 |
| ---- | -------------------------------------------------------- |
| `c`  | **创建**新归档文件（Create）。                           |
| `x`  | **提取**归档中的文件（eXtract）。                        |
| `t`  | **列出**归档内容（Table）。                              |
| `r`  | **追加**文件到已存在的归档中（Append）。                 |
| `u`  | **更新**归档中已存在的文件（Update，仅当源文件较新时）。 |

| 选项                | 功能描述                                                     |
| ------------------- | ------------------------------------------------------------ |
| `z`                 | 通过 **gzip** 压缩 / 解压缩（生成 `.tar.gz` 或 `.tgz` 文件）。 |
| `j`                 | 通过 **bzip2** 压缩 / 解压缩（生成 `.tar.bz2` 文件）。       |
| `J`                 | 通过 **xz** 压缩 / 解压缩（生成 `.tar.xz` 文件）。           |
| `v`                 | **详细输出**操作过程（Verbose）。                            |
| `f`                 | 指定**归档文件名**（File），必须紧跟文件名（如 `archive.tar`）。 |
| `C`                 | **指定目标目录**（Change directory），用于解压或创建归档时。 |
| `p`                 | **保留文件权限**（Preserve permissions）。                   |
| `--exclude=PATTERN` | **排除**匹配模式的文件或目录。                               |

```bash
# 创建

# 创建未压缩的 tar 归档
tar -cf archive.tar dir1/ file.txt  # 将 dir1 和 file.txt 打包为 archive.tar

# 创建 gzip 压缩的 tar 归档（最常见）
tar -czf archive.tar.gz dir1/  # 压缩目录 dir1 为 archive.tar.gz

# 创建 bzip2 压缩的 tar 归档（更高压缩率）
tar -cjf archive.tar.bz2 file1 file2  # 压缩多个文件

# 创建 xz 压缩的 tar 归档（最高压缩率，现代推荐）
tar -cJf archive.tar.xz large_dir/  # 压缩大目录

# 解压缩
# 解压到当前目录
tar -xzf archive.tar.gz  # 解压 gzip 压缩的归档

# 解压到指定目录
mkdir -p /tmp/extract
tar -xzf archive.tar.gz -C /tmp/extract  # 解压到 /tmp/extract

# 只解压归档中的特定文件
tar -xzf archive.tar.gz path/to/file.txt  # 只解压 file.txt

# 查看归档内容但不解压
tar -tzvf archive.tar.gz  # 列出所有文件（t = list）

# 追加或更新归档
# 追加新文件到已有的归档（不压缩）
tar -rf archive.tar new_file.txt  # 追加 new_file.txt 到 archive.tar

# 更新归档中已存在的文件（仅当源文件较新时）
tar -uf archive.tar existing_file.txt  # 更新 existing_file.txt

# 排除特定文件或目录
# 创建归档时排除 .log 文件和 tmp 目录
tar -czf backup.tar.gz --exclude="*.log" --exclude="tmp" /home/user

```

## 文件查看与编辑

### cat

查看文件内容

```bash
cat 文件名 	# 输出文件内容到终端
cat -n 文件名	# 输出文件内容并编写行号，输出到终端

```

### nl

行号查看

相当于`cat -n 文件名`

### more

```bash
more 选项 文件名
```

| 选项   | 功能描述                                                     |
| ------ | ------------------------------------------------------------ |
| `-d`   | 在底部提示行显示帮助信息（如 `[Press space to continue, 'q' to quit.]`），并忽略无效按键。 |
| `-l`   | 忽略文件中的换页符（`^L`，通常用于分隔文档章节）。           |
| `-f`   | 按**行数**而非屏幕高度分页（适用于包含长行的文件）。         |
| `-p`   | 显示新页前**清屏**（替代滚动显示，使页面更清晰）。           |
| `-c`   | 类似 `-p`，但不清屏，而是从顶部开始刷新内容。                |
| `-num` | 指定每页显示的**行数**（如 `-10` 表示每页 10 行）。          |

| 按键           | 功能                                       |
| -------------- | ------------------------------------------ |
| **`空格`**     | 向下翻一页                                 |
| **`Enter`**    | 向下滚动一行                               |
| **`b`**        | 向上翻一页                                 |
| **`f`**        | 向下翻一页（同 `空格`）                    |
| **`q` 或 `Q`** | 退出查看                                   |
| **`/关键词`**  | 向前搜索关键词（按 `n` 查找下一个）        |
| **`:`**        | 进入命令模式（可输入 `n` 或 `p` 切换文件） |

### less

 分页查看文件内容

| 按键           | 功能                                       |
| -------------- | ------------------------------------------ |
| **`空格`**     | 向下翻一页                                 |
| **`Enter`**    | 向下滚动一行                               |
| **`b`**        | 向上翻一页                                 |
| **`f`**        | 向下翻一页（同 `空格`）                    |
| **`q` 或 `Q`** | 退出查看                                   |
| **`/关键词`**  | 向前搜索关键词（按 `n` 查找下一个）        |
| **`:`**        | 进入命令模式（可输入 `n` 或 `p` 切换文件） |

### head

显示文件前几行

```bash
head 文件名 
head 文件名 -n 数字 # 指定行数输出
```

### tail

显示文件后几行，注意-f参数

| 选项        | 功能描述                                                     |
| ----------- | ------------------------------------------------------------ |
| `-n N`      | 显示文件的最后 **N 行**（默认 10 行）。例如：`-n 20` 显示最后 20 行。 |
| `-f`        | **实时跟踪文件更新**（Follow mode），文件有新内容写入时自动显示。 |
| `-F`        | 类似 `-f`，但在文件被删除或重命名后仍尝试继续跟踪（通过文件名）。 |
| `-s SEC`    | 与 `-f` 结合使用，指定检查文件更新的间隔时间（秒），默认约 1 秒。 |
| `--pid=PID` | 与 `-f` 结合使用，当指定的进程（PID）结束时，自动停止 `tail`。 |
| `-q`        | **不显示文件名标题**，适用于同时查看多个文件时。             |

```bash
tail 选项 文件名
```

### grep

搜索文本内容

| 选项         | 功能描述                                                     |       |
| ------------ | ------------------------------------------------------------ | ----- |
| `-i`         | **忽略大小写**（Case-insensitive）。                         |       |
| `-v`         | **反向匹配**，只输出不匹配的行（Invert match）。             |       |
| `-r` 或 `-R` | **递归搜索目录**（Recursive），遍历所有子目录中的文件。      |       |
| ==`-n`==     | ==**显示行号**（Line number），便于定位匹配内容。==          |       |
| `-w`         | **全词匹配**（Word-regexp），只匹配完整单词而非部分。        |       |
| `-c`         | **统计匹配行数**（Count），只输出匹配的行数而非具体内容。    |       |
| `-l`         | **只显示文件名**（Files-with-matches），不显示匹配的行内容。 |       |
| `-A N`       | **显示匹配行及其后 N 行**（After context），用于查看上下文。 |       |
| `-B N`       | **显示匹配行及其前 N 行**（Before context）。                |       |
| `-C N`       | **显示匹配行及其前后各 N 行**（Context），等价于 `-A N -B N`。 |       |
| `-E`         | **使用扩展正则表达式**（Extended regexp），支持更多元字符（如 `+`、` | `）。 |
| `-F`         | **固定字符串匹配**（Fixed strings），不解析正则表达式，速度更快。 |       |

**正则表达式基础**

`grep` 默认使用基本正则表达式（BRE），常用元字符：

- `.`：匹配任意单个字符。
- `*`：匹配前面的字符零次或多次。
- `^`：匹配行首（如 `^hello` 匹配以 `hello` 开头的行）。
- `$`：匹配行尾（如 `world$` 匹配以 `world` 结尾的行）。
- `[]`：匹配方括号内的任意字符（如 `[aeiou]` 匹配任一元音字母）。
- `[^]`：匹配不在方括号内的任意字符（如 `[^0-9]` 匹配非数字字符）。

```bash
grep "error" app.log  			# 在 app.log 中查找包含 "error" 的行（区分大小写）
grep -i "ERROR" app.log  		# 忽略大小写查找
grep -w "error" app.log  		# 只匹配完整单词 "error"，而非 "error_code"

grep -r "config" /etc/  		# 在 /etc 目录及其子目录中搜索包含 "config" 的行
grep -r -l "password" .  		# 只显示包含 "password" 的文件名（不显示具体内容）

grep -n "ERROR" app.log  		# 显示匹配行的行号
grep -C 3 "ERROR" app.log  		# 显示匹配行及其前后各3行

grep "^[0-9]" file.txt  			# 匹配以数字开头的行
grep -E "error|warning" app.log  	# 使用扩展正则表达式，匹配 "error" 或 "warning"
grep -E "[0-9]{3}" file.txt  		# 匹配包含3个连续数字的行

grep -r --exclude="*.log" "pattern" .  				# 递归搜索，但排除 .log 文件
grep -r --exclude-dir="node_modules" "import" .  	# 排除 node_modules 目录

ps aux | grep "nginx"  			# 搜索正在运行的 nginx 进程
cat file.txt | grep "pattern"  	# 等价于 grep "pattern" file.txt


```

### awk

是一个强大的文本处理工具，它以行为单位、以字段为基础处理文本，支持条件判断、循环和自定义函数，非常适合处理结构化文本（如 CSV、日志文件）。

```bash
awk [选项] '模式 {动作}' 文件1 文件2 ...
```

| 变量        | 含义                                                  |
| ----------- | ----------------------------------------------------- |
| `$0`        | 当前整行文本                                          |
| `$1, $2...` | 当前行的第 1、2... 个字段                             |
| `NF`        | 当前行的字段数量（Number of Fields）                  |
| `NR`        | 当前处理的行号（Number of Rows）                      |
| `FS`        | 输入字段分隔符（Field Separator），默认是空格或制表符 |
| `OFS`       | 输出字段分隔符（Output Field Separator），默认是空格  |

**模式与动作**

- **模式**：可以是正则表达式、条件表达式或特殊标记（如 `BEGIN`、`END`）。
- **动作**：用 `{}` 包裹的命令，支持变量赋值、打印、循环等。

```bash
# 打印每行的第1和第3个字段
echo "hello world awk" | awk '{print $1, $3}'  # 输出: hello awk

# 以冒号为分隔符，打印/etc/passwd的第1和第3个字段（用户名和UID）
awk -F: '{print $1, $3}' /etc/passwd

# 打印UID大于1000的用户
awk -F: '$3 > 1000 {print $1}' /etc/passwd

# 打印包含"ERROR"的行
awk '/ERROR/ {print}' log.txt

# 计算文件大小总和（第5列为文件大小）
ls -l | awk 'NR > 1 {sum += $5} END {print "Total:", sum}'

# 计算平均值（假设文件每行一个数字）
awk '{sum += $1} END {print "Average:", sum/NR}' numbers.txt

# 格式化输出，指定列宽和对齐方式
ls -l | awk 'NR > 1 {printf "%-10s %8s\n", $3, $5}'
```

 **BEGIN 和 END 块**

- `BEGIN`：在处理所有行之前执行一次。
- `END`：在处理完所有行之后执行一次。

```bash
awk 'BEGIN {print "开始处理"} {print $0} END {print "处理结束"}' file.txt

# 字符串长度
awk '{print length($0)}' file.txt

# 字符串替换
awk '{gsub(/old/, "new"); print}' file.txt

# if语句
awk '{if ($1 > 10) print $0}' file.txt

# for循环
awk '{for (i=1; i<=NF; i++) print $i}' file.txt
```



## 权限、用户和组管理

### chmod

`chmod`（Change Mode）是一个用于修改文件或目录权限的基础命令。通过调整文件的访问权限，可以控制谁（用户、组、其他）可以对文件进行读取、写入或执行操作，是系统安全和文件管理的核心工具之一。

```bash
chmod [选项] 权限模式  文件/目录...
```

**数字表示法（最常用）**

权限用三位八进制数表示，每一位对应一类用户：

- **第一位**：用户（User，文件所有者）的权限。
- **第二位**：组（Group）的权限。
- **第三位**：其他（Other）用户的权限。

每个位置的权限值通过累加计算：

- `4`：读取权限（Read，`r`）。
- `2`：写入权限（Write，`w`）。
- `1`：执行权限（Execute，`x`）。
- `0`：无权限。

```bash
chmod 755 file.sh    # 所有者：读+写+执行（4+2+1=7），组和其他：读+执行（4+1=5）
chmod 644 config.txt # 所有者：读+写（4+2=6），组和其他：只读（4）
chmod 700 secret.sh  # 仅所有者有读+写+执行权限，组和其他无任何权限
```

**符号表示法（更灵活）**

通过 `+`、`-`、`=` 操作符调整权限：

- **用户类型**：`u`（用户）、`g`（组）、`o`（其他）、`a`（所有，默认）。
- **权限操作**：`+`（添加）、`-`（移除）、`=`（设置）。
- **权限类型**：`r`（读）、`w`（写）、`x`（执行）。

```bash
chmod u+x file.sh     # 给所有者添加执行权限
chmod g-w,o-w config  # 移除组和其他用户的写权限
chmod a=rwx directory # 给所有用户设置读+写+执行权限
chmod o= file.txt     # 移除其他用户的所有权限
```

### chown

修改文件所有者

```bash
chown [选项] [所有者][:组] 文件/目录...
```

| 选项                   | 功能描述                                                 |
| ---------------------- | -------------------------------------------------------- |
| `-R`                   | **递归修改**目录及其所有子内容的所有者和组。             |
| `-v`                   | **显示详细过程**（Verbose），输出每个文件的修改情况。    |
| `-c`                   | 类似 `-v`，但**仅显示实际发生变化的文件**。              |
| `--from=旧所有者:旧组` | **条件修改**，仅当文件的旧所有者和旧组匹配时才进行修改。 |

```bash
# 修改所有者（用户）
chown user1 file.txt  # 将 file.txt 的所有者改为 user1
chown user1 dir/      # 将目录 dir 的所有者改为 user1（不递归）
chown -R user1 dir/   # 递归修改目录及其所有内容的所有者为 user1

#  同时修改所有者和组
chown user1:group1 file.txt  # 将所有者改为 user1，组改为 group1
chown user1: file.txt        # 只改所有者，保留原组（等价于 chown user1 file.txt）
chown :group1 file.txt       # 只改组，保留原所有者

# 使用组名或 GID
chown user1:1001 file.txt  # 使用 GID 1001 作为组
chown :1001 file.txt       # 只改组为 GID 1001
```

### chgrp

修改文件所属组

```bash
chgrp [选项] 组  文件/目录...
```

```bash
chgrp developers project.txt  				# 将 project.txt 的组改为 developers
chgrp -R www-data /var/www/html/  			# 将网站目录及其所有文件的组改为 www-data
chgrp --reference=template.txt config.ini  	# 将 config.ini 的组改为 template.txt 的组

$ ls -l file.txt
-rw-r--r-- 1 owner group 1024 Jul  9 14:00 file.txt
# 解释：
# owner：文件所有者
# group：文件所属组
```

| 选项                   | 功能描述                                               |
| ---------------------- | ------------------------------------------------------ |
| `-R`                   | **递归修改**目录及其所有子内容的组。                   |
| `-v`                   | **显示详细过程**（Verbose），输出每个文件的修改情况。  |
| `-c`                   | 类似 `-v`，但**仅显示实际发生变化的文件**。            |
| `--reference=参考文件` | **以参考文件为模板**，修改目标文件的组为参考文件的组。 |



### whoami

显示当前用户

### id 

显示用户 ID 信息

### groups

显示用户所属的组

### sudo

以管理员权限运行命令



## 用户/组管理管理

### mac没有⚠️useradd 添加新用户

`useradd`（或 `adduser`）是用于创建新用户账户的基础命令。通过该命令，系统管理员可以精确控制用户的配置信息，包括用户名、家目录、默认 shell、用户组等

```bash
useradd [选项] 用户名
```

| 选项             | 功能描述                                                     |
| ---------------- | ------------------------------------------------------------ |
| `-d 目录`        | 指定用户的**家目录**（Home Directory），例如 `-d /home/newuser`。 |
| `-m`             | **自动创建家目录**（若不存在），通常与 `-d` 结合使用。       |
| `-g 组名`        | 指定用户的**初始主组**（Primary Group），组必须已存在。      |
| `-G 组名1,组名2` | 指定用户的**附加组**（Supplementary Groups），用户可同时属于多个组。 |
| `-s shell`       | 指定用户的**默认 shell**，例如 `-s /bin/bash` 或 `-s /sbin/nologin`。 |
| `-c "注释"`      | 添加用户的**描述信息**（Comment），通常用于记录用户身份或职责。 |
| `-u UID`         | 指定用户的**UID**（User ID），需确保 UID 唯一且未被系统保留。 |
| `-e 日期`        | 设置用户账户的**过期日期**（格式：YYYY-MM-DD），例如 `-e 2025-12-31`。 |
| `-f 天数`        | 设置用户密码过期后的**宽限期**（天数），`-f 0` 表示密码过期后立即锁定账户。 |

```bash
useradd -m developer  # 创建名为 developer 的用户，自动创建 /home/developer 目录
useradd -r -s /sbin/nologin nginx  # 创建用于运行nginx服务的系统用户
useradd -m -g users -G sudo,adm -s /bin/bash admin  # 创建管理员用户，加入sudo和adm组
useradd -m -e 2025-12-31 temp_user  # 创建临时用户，账户将于2025年12月31日过期
```

创建用户后，系统会修改以下关键文件：

- **`/etc/passwd`**：存储用户基本信息（用户名、UID、GID、家目录、shell 等）。
- **`/etc/shadow`**：存储用户加密后的密码和密码策略。
- **`/etc/group`**：存储用户组信息，若创建新组，会在此文件中添加记录。
- **`/etc/gshadow`**：存储用户组的加密密码（若有）。

```bash
# 查看用户信息
grep 'developer' /etc/passwd  # 输出类似：developer:x:1001:1001::/home/developer:/bin/bash

# 检查用户家目录
ls -ld /home/developer  # 确保目录已创建且权限正确

# 查看用户所属组
id developer  # 输出用户的UID、主组和附加组信息

# 查看用户信息
grep 'developer' /etc/passwd  # 输出类似：developer:x:1001:1001::/home/developer:/bin/bash

# 检查用户家目录
ls -ld /home/developer  # 确保目录已创建且权限正确

# 查看用户所属组
id developer  # 输出用户的UID、主组和附加组信息

passwd developer  # 为 developer 用户设置密码（需root权限）
```



### mac没有⚠️usermod 修改用户信息（如更改组、shell、主目录）



`usermod` 是用于修改现有用户账户属性的命令。它可以灵活调整用户的配置信息，包括用户名、家目录、用户组、登录权限等，是系统管理员管理用户生命周期的重要工具。

```bash
usermod [选项] 用户名
```

| 选项             | 功能描述                                                     |
| ---------------- | ------------------------------------------------------------ |
| `-l 新用户名`    | **修改用户名**（Login name），例如 `usermod -l new_name old_name`。 |
| `-d 目录 -m`     | **修改家目录**并**迁移现有文件**到新位置（需同时使用 `-m`）。 |
| `-g 组名`        | **修改用户的主组**（Primary Group），组必须已存在。          |
| `-G 组名1,组名2` | **修改用户的附加组**，覆盖原有附加组。例如 `-G sudo,adm`。   |
| `-a -G 组名`     | **追加用户到附加组**（不覆盖原有附加组），例如 `-a -G docker`。 |
| `-s shell`       | **修改用户的默认 shell**，例如 `-s /bin/bash` 或 `-s /sbin/nologin`。 |
| `-u UID`         | **修改用户的 UID**，需确保 UID 唯一且未被系统保留。          |
| `-e 日期`        | **设置用户账户的过期日期**（格式：YYYY-MM-DD），例如 `-e 2025-12-31`。 |
| `-f 天数`        | **设置用户密码过期后的宽限期**（天数），`-f 0` 表示密码过期后立即锁定账户。 |
| `-L`             | **锁定用户账户**（Lock），禁止用户登录（通过在 `/etc/shadow` 中添加 `!`）。 |
| `-U`             | **解锁用户账户**（Unlock），恢复用户登录权限。               |

```bash
usermod -l new_username old_username  # 将用户 old_username 重命名为 new_username
usermod -d /home/new_home -m username  # 将用户的家目录迁移到新位置并保留文件
# 将用户添加到 docker 组（用于使用 Docker）
usermod -a -G docker username

# 修改用户的主组为 developers
usermod -g developers username

usermod -L username  # 锁定用户账户，禁止登录
usermod -U username  # 解锁用户账户，恢复登录权限

usermod -e 2025-12-31 temp_user  # 设置临时用户账户在2025年12月31日过期

# 查看用户信息
id username  # 显示用户的UID、主组和附加组
grep 'username' /etc/passwd  # 查看用户的家目录、shell等信息
chage -l username  # 查看用户的密码和账户过期信息
```



### passwd 设置或修改用户密码

```bash
passwd [选项] [用户名]
```

- **普通用户**：直接运行 `passwd` 可修改自己的密码（无需参数）。
- **管理员**：运行 `passwd 用户名` 可重置指定用户的密码（需 root 权限）。

| 选项      | 功能描述                                                     |
| --------- | ------------------------------------------------------------ |
| `-l`      | **锁定用户账户**（Lock），禁止用户登录（在 `/etc/shadow` 中密码前加 `!`）。 |
| `-u`      | **解锁用户账户**（Unlock），恢复用户登录权限。               |
| `-d`      | **删除用户密码**（Delete），使用户无需密码即可登录（不推荐）。 |
| `-e`      | **强制用户下次登录时修改密码**（Expire）。                   |
| `-S`      | **显示用户密码状态**（Status），包括是否锁定、密码过期时间等。 |
| `--stdin` | **从标准输入读取密码**（仅管理员可用），适用于脚本自动化。   |

```bash
passwd
# 系统会提示输入当前密码（若已设置），然后输入新密码两次进行确认

sudo passwd username  # 为用户 username 设置新密码

sudo passwd -l username  # 锁定账户，禁止登录
sudo passwd -u username  # 解锁账户，恢复登录权限

sudo passwd -e username  # 使密码过期，用户下次登录时必须设置新密码

echo "new_password" | sudo passwd --stdin username
```



### mac没有⚠️chage 管理用户密码过期策略

`chage`（Change Age）是一个用于管理用户密码有效期和账户过期信息的命令。它允许系统管理员精确控制用户密码的更新频率、账户锁定时间等安全策略，是企业级系统中实现合规性和安全性的重要工具。

```bash
chage [选项] 用户名
```



| 选项      | 功能描述                                                     |
| --------- | ------------------------------------------------------------ |
| `-d 日期` | **设置上次密码修改日期**（Date），格式为 YYYY-MM-DD 或天数（如 0 表示今天）。 |
| `-m 天数` | **设置密码修改的最小间隔**（Minimum），即用户必须等待多少天才能再次修改密码。 |
| `-M 天数` | **设置密码的最大有效期**（Maximum），超过此天数后用户必须修改密码。 |
| `-W 天数` | **设置密码过期前的警告期**（Warning），提前多少天提醒用户密码即将过期。 |
| `-I 天数` | **设置密码过期后的宽限期**（Inactive），密码过期后仍可登录的天数，超过则锁定账户。 |
| `-E 日期` | **设置账户的过期日期**（Expire），格式为 YYYY-MM-DD 或天数（如 -1 表示永不过期）。 |
| `-l`      | **显示用户的密码和账户过期信息**（List）。                   |

```bash
chage -l username  # 显示用户 username 的密码和账户过期信息
# 输出示例：
# Last password change					: Jul 10, 2025
# Password expires					: Sep 08, 2025
# Password inactive					: Oct 08, 2025
# Account expires					: never
# Minimum number of days between password change	: 0
# Maximum number of days between password change	: 60
# Number of days of warning before password expires	: 7

chage -M 90 username  # 用户必须每 90 天修改一次密码

chage -d 0 username  # 将上次密码修改日期设为今天，迫使用户下次登录时重置密码
```



### who 显示当前在线用户



- **用户名**：登录的用户账号（如 `user1`、`user2`）。

- 终端类型

  ：

  - `ttyN`：本地虚拟终端（如 `tty1` 是第一个控制台终端）。
  - `pts/N`：伪终端（如通过 SSH 或图形界面终端模拟器登录的会话）。

- **登录时间**：用户登录的日期和时间。

- **来源信息**：括号内显示登录来源（如 `:0` 表示本地图形界面，IP 地址表示远程登录）。

| 选项 | 功能描述                                                    |
| ---- | ----------------------------------------------------------- |
| `-a` | **显示所有信息**（All），包括用户、空会话、系统进程等。     |
| `-b` | **显示系统最后一次启动的时间**（Boot time）。               |
| `-H` | **显示标题行**（Header），标明各列含义。                    |
| `-q` | **仅显示用户数量和用户名**（Quick），不显示登录时间和终端。 |
| `-u` | **显示用户状态**（User），包括空闲时间、JCPU、PCPU 等。     |



### w 显示详细的当前在线用户信息

`w` 是一个用于查看当前登录用户及其活动状态的命令，功能比 `who` 更详细，不仅能显示用户登录信息，还能展示他们正在执行的命令、系统负载等关键数据，是系统管理员监控用户行为和系统状态的常用工具。

```bash
$ w
 14:30:15 up 2 days,  4:10,  3 users,  load average: 0.15, 0.20, 0.18
```

- `14:30:15`：当前系统时间。
- `up 2 days, 4:10`：系统运行时间（已开机 2 天 4 小时 10 分钟）。
- `3 users`：当前登录的用户总数。
- `load average: 0.15, 0.20, 0.18`：系统过去 1 分钟、5 分钟、15 分钟的平均负载（数值越低，系统越空闲）。

```bash
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU  WHAT
alice    tty1                      09:15    5:10m  0.32s  0.32s  -bash
bob      pts/0    192.168.1.102    10:30    0.00s  0.15s  0.05s  sshd: bob [priv]
carol    pts/1    10.0.0.5         13:45    10.0s  0.20s  0.20s  vim /etc/nginx/nginx.conf
```

- **USER**：登录的用户名。
- **TTY**：用户登录的终端（`ttyN` 为本地虚拟终端，`pts/N` 为远程 / 图形终端）。
- **FROM**：登录来源（远程登录显示 IP 地址，本地登录可能为空或显示 `:0`）。
- **LOGIN@**：登录时间（格式为 `HH:MM`）。
- **IDLE**：用户空闲时间（自上次输入后未操作的时长，`m` 表示分钟，`s` 表示秒）。
- **JCPU**：该终端（TTY）上所有进程消耗的总 CPU 时间（包括后台进程）。
- **PCPU**：当前活跃进程（WHAT 列显示的命令）消耗的 CPU 时间。
- **WHAT**：用户正在执行的命令。

| 选项 | 功能描述                                                     |
| ---- | ------------------------------------------------------------ |
| `-h` | 不显示首行的系统汇总信息（仅显示用户列表）。                 |
| `-u` | 忽略用户名的 DNS 反向解析（加速输出，尤其在远程用户较多时）。 |
| `-s` | 简化输出格式（仅保留 USER、TTY、FROM、LOGIN@、WHAT 列）。    |
| `-f` | 显示用户登录的来源（如远程 IP），默认已包含此信息（部分系统需显式指定）。 |
| `-V` | 显示命令版本信息。                                           |

### last 显示最近的登录记录

`last` 命令用于查看用户的登录历史记录，包括登录时间、注销时间、登录终端、来源 IP 等信息，是系统管理员审计用户行为、追踪登录活动的重要工具。其数据主要来自 `/var/log/wtmp` 文件（记录所有登录和注销事件）。

```bash
last [选项] [用户名/终端名]
```

```bash
$ last
root     pts/0        192.168.1.102    Wed Jul  9 10:30   still logged in
user1    tty1                          Tue Jul  8 09:15 - 18:45  (09:30)
user2    pts/1        203.0.113.5      Mon Jul  7 14:20 - 15:30  (01:10)
reboot   system boot  5.4.0-100-generic Mon Jul  7 10:00   still running
root     pts/0        198.51.100.8     Sun Jul  6 20:15 - down   (02:30)
...
wtmp begins Sun Jul  6 00:00:00 2025
```

- **第一列**：用户名（如 `root`、`user1`）或特殊事件（如 `reboot` 表示系统重启）。
- **第二列**：登录终端（`ttyN` 为本地终端，`pts/N` 为远程终端，`system boot` 对应重启事件）。
- **第三列**：登录来源（远程登录显示 IP 地址，本地登录可能为空或显示终端名）。
- **第四至六列**：登录时间（`Wed Jul 9 10:30`）。
- **第七至八列**：注销时间或状态（`still logged in` 表示未注销，`down` 表示系统关机，`- 18:45` 表示注销时间）。
- **最后一列**：登录持续时长（如 `(09:30)` 表示持续 9 小时 30 分钟）。

| 选项        | 功能描述                                                     |
| ----------- | ------------------------------------------------------------ |
| `-n <数字>` | 限制显示的记录行数（如 `last -n 5` 显示最近 5 条记录）。     |
| `-f <文件>` | 读取指定文件作为数据源（默认读取 `/var/log/wtmp`，可指定 `/var/log/btmp` 查看失败登录记录）。 |
| `-t <时间>` | 显示指定时间之前的记录（时间格式为 `YYYYMMDDHHMMSS`，如 `last -t 20250708120000`）。 |
| `-i`        | 将 IP 地址显示为数字格式（而非主机名，避免 DNS 解析延迟）。  |
| `-x`        | 显示系统关机、重启等系统事件的记录（默认可能不显示）。       |
| `-R`        | 不显示登录来源的主机名或 IP 地址。                           |
| `-w`        | 显示完整的用户名和主机名（默认可能截断长名称）。             |

### whoami 

显示当前用户

### mac没有⚠️userdel 删除用户

| `-r` | **递归删除用户的家目录和邮件**（Remove home directory and mail spool）。 |
| ---- | ------------------------------------------------------------ |
| `-f` | **强制删除**（Force），即使用户当前已登录或家目录非空也会尝试删除。 |
| `-Z` | **删除 SELinux 用户映射**（仅在启用 SELinux 的系统中需要）。 |

```bash
userdel username  # 删除用户账户，但保留其家目录（/home/username）和邮件
userdel -r username  # 删除用户账户，并递归删除家目录和邮件（/var/spool/mail/username）
userdel -f -r username  # 强制删除用户（即使已登录），并删除家目录

# 确认用户已从 passwd 文件中移除
grep username /etc/passwd  # 无输出表示删除成功

# 检查家目录是否已删除（若使用 -r 选项）
ls -ld /home/username  # 应显示 "No such file or directory"
```



### mac没有⚠️groupadd 添加新组

`groupadd` 是用于创建新用户组的命令。通过该命令，系统管理员可以灵活管理用户组，为用户分配不同的权限和资源访问级别。

```bash
groupadd [选项] 组名
```

| 选项       | 功能描述                                                     |
| ---------- | ------------------------------------------------------------ |
| `-g GID`   | 指定组的**GID**（Group ID），需确保 GID 唯一且未被系统保留。 |
| `-r`       | 创建**系统组**（System group），GID 通常分配在 1-999 范围内（取决于系统配置）。 |
| `-f`       | **强制创建**（Force），若组已存在则不报错直接退出（部分系统支持）。 |
| `-K 键=值` | 覆盖 `/etc/login.defs` 中的默认设置（如 `-K GID_MIN=10000`）。 |

```bash
groupadd developers  # 创建名为 developers 的用户组，系统自动分配下一个可用GID
groupadd -r nginx  # 创建用于运行nginx服务的系统组

groupadd -g 2000 admins  # 创建 admins 组，指定GID为2000

# 查看用户组信息
grep 'developers' /etc/group  # 输出类似：developers:x:1002:

# 检查GID是否正确
getent group developers  # 输出：developers:x:1002:
```





### mac没有⚠️groupdel 删除组

```bash
groupdel [选项] 组名
```

`groupdel` 用于删除指定的用户组，但需满足以下条件：

1. **组必须存在**：若组不存在，命令会报错。
2. **组内不能有用户**：若组是某个用户的主组（Primary Group），则无法直接删除。需先将用户的主组修改为其他组，或删除该用户。

```bash
groupdel developers  # 删除名为 developers 的用户组
groupdel developers
# 报错信息：
# groupdel: cannot remove the primary group of user 'alice'
# 方法1：将用户的主组修改为其他组
usermod -g users alice  # 将 alice 的主组改为 users
groupdel developers     # 再次尝试删除

# 方法2：删除用户（谨慎操作）
userdel -r alice        # 删除用户及其家目录
groupdel developers     # 成功删除组

# 检查组是否已删除
grep 'developers' /etc/group  # 无输出表示删除成功

# 使用 getent 命令确认
getent group developers  # 无输出表示组不存在
```



### mac没有⚠️gpasswd 添加或删除用户到组

```bash
gpasswd [选项] 组名
```

| 选项          | 功能描述                                                     |
| ------------- | ------------------------------------------------------------ |
| `-a 用户名`   | **添加用户到组**（Add user to group）。                      |
| `-d 用户名`   | **从组中删除用户**（Delete user from group）。               |
| `-M 用户列表` | **批量设置组成员**（Members），覆盖原有成员（格式：`user1,user2,user3`）。 |
| `-r`          | **移除组密码**（Remove password），允许非密码用户通过 `newgrp` 临时加入组。 |
| `-A 用户列表` | **设置组管理员**（Administrators），赋予指定用户管理组的权限。 |
| `-R`          | **限制组访问**（Restrict），仅允许密码用户通过 `newgrp` 加入组。 |

```bash
gpasswd -a alice developers  # 将用户 alice 添加到 developers 组
gpasswd -d bob developers  # 将用户 bob 从 developers 组中删除
gpasswd -M alice,bob,charlie developers  # 设置 developers 组的成员为 alice、bob、charlie
gpasswd developers  # 为 developers 组设置密码（交互模式）
# 输入新组密码并确认

gpasswd -r developers  # 移除 developers 组的密码

# 查看组成员
getent group developers  # 输出示例：developers:x:1002:alice,bob

# 检查组密码和管理员（/etc/gshadow 文件）
grep 'developers' /etc/gshadow  # 输出示例：
# developers:!::alice  # 密码被禁用（!），alice 是管理员
```



mac没有⚠️usermod -aG 添加用户到附加组

### groups 

显示用户所属的组





## 进程管理

### ps 

```bash
ps 					# 显示进程列表

ps x

ps aux  			# 显示所有用户进程
```

![psx进程状态](.\psx进程状态.png)

### top 

实时查看进程状态 q退出

### kill 终止进程（需进程 ID）

kill的进程id可以用ps进行查看

![kill与killall信号](E:\kecheng\linux笔记\kill与killall信号.png)

### pkill 

按名称终止进程

### killall 

终止所有指定名称的进程



### jobs 

显示后台任务

### fg 

将后台任务切换到前台

### bg 

继续后台任务

jobs 与 bg fg 相配合，jobs 由作业编号，通过bg、fg调度

```bash
jobs  # [1]+  运行中       sleep 100 &
fg %1
bg %1
```



### nohup 让进程在后台持续运行

`nohup`（No Hangup）是一个用于让命令在后台持续运行的工具，即使用户退出终端或会话关闭。它特别适用于需要长时间执行的任务（如下载、编译、备份等），避免因网络中断或用户误操作导致任务中断。

```bash
nohup 命令 [参数] &
```

```bash
nohup wget https://example.com/large_file.zip &  # 长时间下载任务
nohup bash my_script.sh &    #后台运行脚本
nohup ping baidu.com > ping.log 2>&1 &  # 自定义输出文件

# > ping.log：将标准输出定向到 ping.log。
# 2>&1：将错误输出（stderr）合并到标准输出（stdout）。
```



### renice 调整进程优先级

`renice` 是用于调整进程优先级的命令。通过修改进程的 **nice 值**（Niceness），可以动态调整进程的 CPU 资源分配，从而优化系统性能或确保关键任务获得更多资源。

**基本概念：nice 值**

- **范围**：`-20`（最高优先级）到 `19`（最低优先级），默认值为 `0`。
- 含义：
  - **负值**：进程获得更多 CPU 时间（高优先级）。
  - **正值**：进程让出 CPU 时间（低优先级）。
- 限制：
  - 普通用户只能将 nice 值调高（降低优先级，如从 `0` 调整为 `10`）。
  - root 用户可以任意调整 nice 值（包括降低至负值）。

```bash
renice [选项] nice值 -p 进程ID   # 按进程ID调整
renice [选项] nice值 -u 用户名   # 按用户调整所有进程
renice [选项] nice值 任务组ID    # 按任务组调整（很少用）
```

| 选项 | 功能描述                                                     |
| ---- | ------------------------------------------------------------ |
| `-n` | 指定 nice 值（可省略，直接写 nice 值），例如 `-n 10` 或 `10`。 |
| `-p` | 按进程 ID（PID）调整，例如 `-p 1234`。                       |
| `-u` | 按用户名调整该用户的所有进程，例如 `-u www-data`。           |
| `-g` | 按进程组 ID（PGID）调整，例如 `-g 5678`。                    |
| `-t` | 按终端（TTY）调整该终端启动的所有进程，例如 `-t pts/0`。     |

### disown 脱离终端

| 选项 | 功能描述                                                     |
| ---- | ------------------------------------------------------------ |
| `-h` | **不终止进程**（仅标记）：即使终端关闭，进程也不会收到 `SIGHUP` 信号。 |
| `-a` | **分离所有进程**：将当前终端的所有后台进程都分离。           |
| `-r` | **仅分离运行中的进程**：忽略已停止的进程（如通过 `Ctrl+Z` 暂停的进程）。 |

```bash
disown [选项] [进程ID]
```

```bash
# 启动一个长时间运行的命令（如下载）
wget https://example.com/large_file.zip &

# 将该进程分离，使其在终端关闭后继续运行
disown %1  # %1 表示第一个作业（Job），可通过 jobs 命令查看作业编号

disown -a  # 分离当前终端的所有后台进程
disown -r  # 只分离正在运行的进程，忽略已停止的进程

# 标记作业1，使其忽略SIGHUP信号（但仍与终端关联）
disown -h %1
```

| 命令     | 特点                                                         | 适用场景                     |
| -------- | ------------------------------------------------------------ | ---------------------------- |
| `disown` | 分离已在运行的后台进程，无需重新启动命令。                   | 临时需要让进程持续运行时。   |
| `nohup`  | 启动命令时直接忽略 SIGHUP 信号，需提前知道要长时间运行。     | 预先规划的长时间任务。       |
| `screen` | 创建虚拟终端，可随时断开 / 重连，任务持续运行，输出保留在 screen 会话中。 | 需要交互式控制的长时间任务。 |
| `tmux`   | 类似 screen，但功能更强大，支持窗口管理和会话共享。          | 需要复杂会话管理的场景。     |

### lsof

`lsof`（List Open Files）是一个强大的工具，用于列出当前系统中所有被打开的文件和资源。由于在 Linux 中，一切皆文件（包括网络套接字、设备驱动等），`lsof` 可以帮助管理员监控资源使用、排查进程异常、解决文件被锁定等问题。

| 选项             | 功能描述                                                     |
| ---------------- | ------------------------------------------------------------ |
| `-p PID`         | 显示指定进程（PID）打开的所有文件。                          |
| `-u 用户`        | 显示指定用户打开的所有文件。                                 |
| `-i [协议:端口]` | 显示网络连接相关的文件（如 `-i :80` 显示所有 80 端口的连接）。 |
| `-t`             | 只输出 PID（常用于脚本自动化）。                             |
| `-a`             | 多个选项之间取交集（默认是并集）。                           |
| `-d 设备号`      | 显示指定设备打开的文件（如 `-d 1,2` 显示标准输出 / 错误对应的文件）。 |
| `-n`             | 不解析主机名（加速输出，避免 DNS 查询）。                    |
| `-P`             | 不解析端口名（直接显示端口号，如显示 80 而非 http）。        |

```bash
lsof /var/log/syslog  # 显示所有打开syslog文件的进程
lsof -p 1234  # 显示PID为1234的进程打开的所有文件
lsof -i :80  # 显示所有占用80端口的进程（如Web服务器）
lsof -u www-data  # 显示www-data用户（如Nginx/Apache）打开的所有文件
lsof -i -n -P  # 显示所有网络连接，不解析主机名和端口名（加速输出）
lsof +L1  # 显示所有被删除（状态为deleted）但仍被进程打开的文件

$ lsof /var/log/syslog
COMMAND   PID     USER   FD   TYPE     DEVICE SIZE/OFF    NODE NAME
rsyslogd  1234  syslog    7w   REG       8,1    12345   12345 /var/log/syslog

# 查找占用80端口的进程并终止
lsof -t -i :80 | xargs kill -9  # 注意：可能终止多个进程，谨慎使用

# 统计每个用户打开的文件数量
lsof | awk '{print $3}' | sort | uniq -c | sort -nr


watch -n 1 'lsof | grep /tmp'  # 每1秒刷新一次，监控/tmp目录下的文件打开情况
```

- **COMMAND**：进程名。
- **PID**：进程 ID。
- **USER**：进程所有者。
- **FD**：文件描述符（如 `cwd` 表示当前工作目录，`txt` 表示程序二进制文件，`7w` 表示文件描述符 7 且以写模式打开）。
- **TYPE**：文件类型（如 `REG` 表示常规文件，`DIR` 表示目录，`IPv4` 表示 IPv4 套接字）。
- **DEVICE**：设备号。
- **SIZE/OFF**：文件大小或偏移量。
- **NODE**：文件的 inode 号。
- **NAME**：文件名或网络地址。

### & 

后台运行





## 网络管理

### ssh

`ssh`（Secure Shell）是一种网络协议，用于通过加密通道安全地远程登录和管理计算机。它取代了早期的不安全协议（如 Telnet、rsh），广泛应用于服务器管理、文件传输和远程命令执行

```bash
ssh [选项] [用户名@]主机名 [命令]
```

**核心功**
**能**

1. **远程登录**：安全地登录到远程服务器。
2. **远程命令执行**：直接在远程服务器上执行命令。
3. **端口转发**：通过 SSH 隧道转发网络流量（如访问内网服务）。
4. **文件传输**：结合 `scp` 或 `sftp` 实现安全的文件传输。

| 选项                            | 功能描述                                                     |
| ------------------------------- | ------------------------------------------------------------ |
| `-p 端口`                       | 指定远程服务器的 SSH 端口（默认 22），例如 `-p 2222`。       |
| `-i 密钥文件`                   | 指定私钥文件路径，用于密钥认证，例如 `-i ~/.ssh/id_rsa`。    |
| `-l 用户名`                     | 指定登录用户名，例如 `-l root`。                             |
| `-v`                            | 启用详细模式（Verbose），用于调试连接问题。                  |
| `-C`                            | 启用压缩，提高传输速度（适用于低带宽场景）。                 |
| `-N`                            | 不执行远程命令（仅用于端口转发）。                           |
| `-f`                            | 后台运行 SSH 进程。                                          |
| `-L 本地端口:目标主机:目标端口` | 本地端口转发（将本地端口流量转发到远程目标）。               |
| `-R 远程端口:目标主机:目标端口` | 远程端口转发（将远程端口流量转发到本地目标，需服务器允许）。 |

```bash
ssh user@example.com  # 以user身份登录example.com服务器
ssh user@example.com "ls -l"  # 登录并执行ls -l命令，然后退出
ssh -i ~/.ssh/id_rsa user@example.com  # 使用指定私钥登录
# 将本地的8080端口流量转发到远程服务器的80端口
ssh -L 8080:localhost:80 user@example.com -N
# 访问 http://localhost:8080 即可访问远程服务器的80端口服务

# 将远程服务器的8080端口流量转发到本地的80端口
ssh -R 8080:localhost:80 user@example.com -N
# 需在远程服务器配置允许GatewayPorts=yes

ssh -C user@example.com  # 启用压缩，提高传输速度
```

配置文件与优化
客户端配置：`~/.ssh/config`

```bash
Host example
    HostName example.com
    User user
    Port 2222
    IdentityFile ~/.ssh/id_rsa
```

使用：ssh example 即可快速登录。
免密登录：

```

ssh-keygen  # 生成密钥对
ssh-copy-id user@example.com  # 将公钥复制到远程服务器
```

保持连接：
修改 ~/.ssh/config 添加：

```

ServerAliveInterval 60
```

### ifconfig/ip

`ifconfig` 和 `ip` 是用于查看和配置网络接口的核心工具

```bash
ifconfig  # 显示所有活跃接口
ifconfig eth0  # 显示指定接口（如eth0）
```

1. 基本功能
   查看网络接口状态：

```bash
ifconfig  # 显示所有活跃接口
ifconfig eth0  # 显示指定接口（如eth0）
```

​	配置 IP 地址（临时）：

```bash
ifconfig eth0 192.168.1.100 netmask 255.255.255.0  # 设置IP和子网掩码
ifconfig eth0 up/down  # 启用/禁用接口
```



2. 输出字段解释
   bash

   ```bash
   2:eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255
        ether 00:11:22:33:44:55  txqueuelen 1000  (Ethernet)
        RX packets 1000  bytes 1000000 (1.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 500  bytes 60000 (60.0 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   ```
   
   

- flags：接口状态（UP = 启用，RUNNING = 连接正常）。
- inet：IPv4 地址。
- netmask：子网掩码。
- broadcast：广播地址。
- ether：MAC 地址。

二、ip（新一代工具）
ip 是 iproute2 套件的一部分，功能更强大，逐步取代 ifconfig。

1. 基本功能
    查看网络接口：
    bash

  ```bash
  ip addr show  # 显示所有接口信息（缩写：ip a）
  ip addr show eth0  # 显示指定接口
  ```

  

配置 IP 地址（临时）：

```bash
ip addr add 192.168.1.100/24 dev eth0  # 添加IP（CIDR格式）
ip addr del 192.168.1.100/24 dev eth0  # 删除IP
ip link set eth0 up/down  # 启用/禁用接口
```

查看路由表：

```bash
ip route show  # 显示路由表（缩写：ip r）
```

添加 / 删除路由：

```bash
ip route add 192.168.2.0/24 via 192.168.1.1  # 添加静态路由
ip route del 192.168.2.0/24  # 删除路由
```



2. 输出字段解释

   ```bash
   2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:11:22:33:44:55 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic eth0
       valid_lft 86398sec preferred_lft 86398sec
    inet6 fe80::211:22ff:fe33:4455/64 scope link
       valid_lft forever preferred_lft forever
   ```
   
   

link/ether：MAC 地址。
inet：IPv4 地址（/24 表示子网掩码 255.255.255.0）。
inet6：IPv6 地址。
state UP：接口状态。
三、ifconfig vs ip 命令对照表四、持久化配置
临时配置（如 ifconfig/ip 命令）重启后失效，需修改配置文件：

| 功能            | ifconfig                                            | ip 命令                                 |
| --------------- | --------------------------------------------------- | --------------------------------------- |
| 查看所有接口    | `ifconfig -a`                                       | `ip addr show`                          |
| 启用 / 禁用接口 | `ifconfig eth0 up/down`                             | `ip link set eth0 up/down`              |
| 设置 IP 地址    | `ifconfig eth0 192.168.1.100 netmask 255.255.255.0` | `ip addr add 192.168.1.100/24 dev eth0` |
| 删除 IP 地址    | 无直接对应                                          | `ip addr del 192.168.1.100/24 dev eth0` |
| 查看 MAC 地址   | `ifconfig eth0`                                     | `ip link show eth0`                     |
| 查看路由表      | `route -n`                                          | `ip route show`                         |
| 添加默认网关    | `route add default gw 192.168.1.1`                  | `ip route add default via 192.168.1.1`  |
| 查看 ARP 表     | `arp -a`                                            | `ip neigh show`                         |

1. Ubuntu/Debian（Netplan）
编辑 /etc/netplan/01-netcfg.yaml：

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      addresses: [192.168.1.100/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

应用配置：

```
sudo netplan apply
```


2. CentOS/RHEL（NetworkManager）
编辑 /etc/sysconfig/network-scripts/ifcfg-eth0：

ini

```ini
TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
ONBOOT=yes
```

重启网络：

```bash
sudo systemctl restart NetworkManager
```

五、总结

| 工具       | 优势                                 | 劣势                           | 推荐场景                 |
| ---------- | ------------------------------------ | ------------------------------ | ------------------------ |
| `ifconfig` | 简单直观，旧系统兼容性好             | 功能有限，不支持 IPv6 高级特性 | 快速查看接口状态         |
| `ip`       | 功能强大，支持路由、策略路由、隧道等 | 语法复杂，学习曲线陡           | 高级网络配置、脚本自动化 |

### telnet 

发送数据

Telnet 是 TCP/IP 协议族中的一员，是 Internet 远程登录服务的标准协议和主要方式。它允许用户通过命令行界面远程登录到其他计算机或设备，在本地计算机上完成远程主机的工作。以下是详细介绍：

- 工作原理：
  - **建立连接**：Telnet 客户端通过 TCP 端口 23（默认）与服务端建立连接，通过 TCP 的三次握手建立可靠连接。
  - **选项协商**：连接建立后，客户端和服务端会协商确定双方支持的功能选项，如终端类型、窗口大小等。客户端发送 “WILL” 或 “DO” 指令表示支持或请求某个功能，服务端回复 “DO” 或 “DONT” 确认或拒绝。
  - **数据传输**：客户端和服务端通过 TCP 连接发送和接收数据，客户端输入的命令会以明文形式发送到服务端，服务端执行命令后将结果返回给客户端。Telnet 使用网络虚拟终端（NVT）作为标准中间格式，屏蔽不同操作系统终端的差异。
  - **连接终止**：客户端或服务端发送 “CLOSE” 命令，触发 TCP 连接的四次挥手，释放资源。
- 功能用途：
  - **远程配置管理**：常用于网络设备的远程配置和管理，管理员可远程连接到网络设备上进行集中管理和维护。
  - **服务端登录实验**：在进行服务端登录实验时，可以使用 Telnet 模拟客户端登录，进行相关实验配置。
  - **网络调试排查**：可用于网络调试和故障排除，例如通过连接到相关设备或服务，查看响应情况来排查网络问题。
- 客户端常用命令：
  - **open**：用于建立到主机的 Telnet 连接，如 “open hostname (port)”，port 默认为 23。
  - **close**：关闭现有的 Telnet 连接。
  - **display**：查看 Telnet 客户端的当前设置。
  - **send**：向 Telnet 服务器发送命令，如 “send ao” 可放弃输出命令。
  - **quit**：退出 Telnet 客户端。
- **局限性**：Telnet 以明文形式传输数据，用户名、密码和命令等容易被窃听，存在安全风险。因此，在现代网络环境中，它逐渐被更安全的 SSH（Secure Shell）协议所取代。不过，由于其简单易用，仍可用于一些对安全性要求不高的场景，或用于测试网络服务的连通性。

| 选项          | 功能描述                                                     |
| ------------- | ------------------------------------------------------------ |
| `-l 用户`     | 指定登录用户名，例如 `telnet -l root example.com`。          |
| `-n 日志文件` | 将网络会话记录到文件中。                                     |
| `-t 终端类型` | 指定终端类型（如 vt100），例如 `telnet -t vt100 example.com`。 |
| `-port 端口`  | 指定连接端口（替代默认的 23），例如 `telnet -port 8080 example.com`。 |

| 命令       | 功能描述                          |
| ---------- | --------------------------------- |
| `help`     | 显示帮助信息。                    |
| `close`    | 关闭当前连接。                    |
| `quit`     | 退出 telnet 程序。                |
| `display`  | 显示当前 telnet 设置。            |
| `send eof` | 发送文件结束符（相当于 Ctrl+D）。 |

### nc

`nc`（Netcat）是一个功能强大的网络工具，被称为 “网络瑞士军刀”。它可以创建 TCP 或 UDP 连接，发送和接收数据，甚至可以用于端口扫描、端口转发、文件传输等。

```bash
nc [选项] [主机] [端口]
```

1. **TCP/UDP 客户端**：连接到远程主机的指定端口。
2. **TCP/UDP 服务器**：监听本地端口，接收连接。
3. **数据传输**：在客户端和服务器之间传输任意数据。
4. **端口扫描**：检查目标主机的端口是否开放。
5. **网络调试**：测试网络连接和服务响应

| 选项      | 功能描述                                                     |
| --------- | ------------------------------------------------------------ |
| `-l`      | 监听模式，作为服务器接收连接（必须）。                       |
| `-p 端口` | 指定本地端口（客户端或服务器）。                             |
| `-u`      | 使用 UDP 协议（默认 TCP）。                                  |
| `-v`      | 详细模式，显示连接信息和错误。                               |
| `-n`      | 不解析主机名和端口名（直接使用 IP 和数字端口），加速连接。   |
| `-z`      | 零输入模式，仅用于端口扫描（不发送数据）。                   |
| `-w 超时` | 设置连接超时时间（秒）。                                     |
| `-e 程序` | 执行指定程序并将其输入 / 输出与网络连接绑定（需 root 权限）。 |

```bash
nc -l 12345  # 监听本地12345端口
nc example.com 12345  # 连接到服务器
# 双方可互相发送文本消息

nc -lu 12345  # 监听UDP 12345端口
nc -u example.com 12345  # 连接到UDP服务器

nc -l 12345 < file.txt  # 将file.txt发送到连接者

nc -vz example.com 80  # 检查80端口是否开放
nc -vz example.com 1-100  # 扫描1-100所有端口（较慢）

nc -l 12345 -e /bin/bash  # 允许客户端执行bash命令  服务器端（危险！）

nc example.com 12345  # 获取远程shell


```

### ping 

测试网络连通性

### curl 

`curl`（Client URL）是一个强大的命令行工具，用于通过各种协议（如 HTTP、HTTPS、FTP 等）传输数据。它支持多种功能，包括发送 HTTP 请求、下载文件、提交表单等，是开发、测试和运维的必备工具。

```bash
curl [选项] [URL]
```

**核心功能**

1. **下载文件**：直接从 URL 获取内容并保存到文件。
2. **发送 HTTP 请求**：支持 GET、POST、PUT、DELETE 等请求方法。
3. **自定义请求头**：添加或修改 HTTP 请求头信息。
4. **处理认证**：支持 Basic、Digest 等多种认证方式。
5. **文件上传**：通过 HTTP 或 FTP 上传文件。

| 选项                  | 功能描述                                                     |
| --------------------- | ------------------------------------------------------------ |
| `-o 文件`             | 将输出保存到指定文件（小写 o），例如 `-o output.txt`。       |
| `-O`                  | 按照服务器端文件名保存（大写 O），例如 `curl -O https://example.com/file.zip`。 |
| `-L`                  | 跟随重定向（如 HTTP 301/302 跳转）。                         |
| `-X 方法`             | 指定 HTTP 请求方法（如 `-X POST`、`-X DELETE`）。            |
| `-d 数据`             | 发送 POST 数据（适用于表单提交），例如 `-d "key=value"`。    |
| `-H 头信息`           | 添加 HTTP 请求头，例如 `-H "Content-Type: application/json"`。 |
| `-i`                  | 显示响应头信息。                                             |
| `-s`                  | 静默模式（不显示进度条），适合脚本使用。                     |
| `-u 用户:密码`        | 添加认证信息，例如 `-u admin:password`。                     |
| `--data-binary @文件` | 上传二进制文件，例如 `--data-binary @image.jpg`。            |
| `--compressed`        | 接受压缩响应（如 gzip、deflate）。                           |





### wget 

`wget` 是一个在 Linux、macOS 和其他类 Unix 系统中常用的命令行工具，用于从网络下载文件。它支持 HTTP、HTTPS 和 FTP 协议，并且具有断点续传、递归下载、后台下载等功能。

| 选项                     | 功能描述                                              |
| ------------------------ | ----------------------------------------------------- |
| `-O 文件名`              | 指定下载文件的名称或路径                              |
| `-c`                     | 断点续传，继续未完成的下载                            |
| `-b`                     | 后台下载（将进程放入后台）                            |
| `-q`                     | 安静模式（不显示下载进度）                            |
| `-v`                     | 详细模式（显示更多下载信息）                          |
| `-nc`                    | 不覆盖已存在的文件（no-clobber）                      |
| `--limit-rate=速度`      | 限制下载速度（例如 `--limit-rate=200k` 表示 200KB/s） |
| `-r`                     | 递归下载（用于下载整个网站或目录，谨慎使用）          |
| `--user-agent=UA`        | 设置用户代理（例如 `--user-agent="Mozilla/5.0"`）     |
| `--no-check-certificate` | 不验证 SSL 证书（在访问自签名 HTTPS 网站时可能需要）  |

```bash
wget https://example.com/file.zip  								# 简单下载
wget -O custom_name.zip https://example.com/original_name.zip 	# 下载并重命名
wget -c https://example.com/large_file.iso 						# 断点续传
wget -b https://example.com/big_file.tar.gz  					# 后台下载大文件 下载信息会输出到 wget-log 文件。

wget --limit-rate=500k https://example.com/file.mp4				# 限制下载速度（适用于带宽有限的环境）
wget -r -np https://example.com/resources/						# 递归下载网站目录（谨慎使用）-np：不追溯到父目录
wget --user=username --password=password https://example.com/protected/file.zip		#下载认证保护的文件
```





### scp 

通过SSH复制文件，`scp`（Secure Copy）是一个基于 SSH 协议的安全文件传输工具，用于在本地和远程主机之间或两台远程主机之间复制文件。它通过加密通道传输数据，避免了传统文件传输工具（如 FTP、Telnet）的安全风险。

```bash
scp [选项] [源文件] [目标路径]
```

**核心功能**

1. **本地到远程传输**：将本地文件复制到远程主机。
2. **远程到本地传输**：从远程主机下载文件到本地。
3. **远程到远程传输**：在两台远程主机之间直接传输文件（需当前主机有访问权限）。
4. **递归复制目录**：一次性复制整个目录及其子目录。
5. **保持文件属性**：保留文件的权限、时间戳等元数据。

| 选项          | 功能描述                                                     |
| ------------- | ------------------------------------------------------------ |
| `-r`          | 递归复制目录（Recursive）。                                  |
| `-P 端口`     | 指定 SSH 端口（大写 P，默认 22），例如 `-P 2222`。           |
| `-i 密钥文件` | 指定私钥文件路径，例如 `-i ~/.ssh/id_rsa`。                  |
| `-C`          | 启用压缩传输（Compress），提高传输速度（适用于低带宽场景）。 |
| `-p`          | 保留文件的权限、时间戳等元数据（Preserve）。                 |
| `-q`          | 静默模式（Quiet），不显示传输进度。                          |

```bash
# 1. 从本地复制到远程主机
# 复制单个文件
scp local_file.txt user@example.com:/path/  # 上传到远程目录
scp local_file.txt user@example.com:/path/remote_name.txt  # 指定远程文件名

# 递归复制目录
scp -r local_dir/ user@example.com:/path/  # 上传整个目录

# 2. 从远程主机下载到本地
# 下载单个文件
scp user@example.com:/path/remote_file.txt .  # 下载到当前目录
scp user@example.com:/path/remote_file.txt local_name.txt  # 指定本地文件名

# 下载目录
scp -r user@example.com:/path/remote_dir/ .  # 下载整个目录

# 3. 两台远程主机之间传输（通过本地中转）
scp user1@host1:/path/file.txt user2@host2:/path/  # 从host1复制到host2

# 4. 使用特定 SSH 端口和密钥
scp -P 2222 -i ~/.ssh/custom_key user@example.com:/path/file.txt .

# 5. 启用压缩传输大文件
scp -C large_file.zip user@example.com:/path/  # 压缩传输，提高速度
```



### rsync 

`rsync` 是一个功能强大的文件同步工具，用于在本地和远程主机之间或两台远程主机之间高效地复制和同步文件。与 `scp` 相比，`rsync` 支持增量传输、断点续传和文件属性保留，特别适合大文件或大量文件的同步场景。

```bash
rsync [选项] [源路径] [目标路径]
```

**核心功能**

1. **增量传输**：仅复制源和目标之间有差异的文件（基于文件大小和时间戳）。
2. **断点续传**：中断的传输可恢复，无需重新开始。
3. **递归同步目录**：完整复制目录结构和子目录。
4. **保持文件属性**：保留权限、所有者、时间戳等元数据。
5. **压缩传输**：通过 SSH 加密传输时压缩数据，减少带宽消耗。
6. **排除指定文件**：选择性地忽略某些文件或目录。

| 选项             | 功能描述                                                     |
| ---------------- | ------------------------------------------------------------ |
| `-a`             | 归档模式（Archive），递归并保留几乎所有文件属性（等同于 `-rlptgoD`）。 |
| `-v`             | 详细模式（Verbose），显示传输进度。                          |
| `-z`             | 压缩传输数据（Compress），适用于网络传输。                   |
| `-h`             | 以人类可读的格式显示数字（如 1K、2M）。                      |
| `-n`             | 模拟运行（Dry-run），显示将要传输的文件但不实际执行。        |
| `--delete`       | 删除目标路径中源路径不存在的文件（同步删除）。               |
| `--exclude 模式` | 排除匹配模式的文件或目录，例如 `--exclude '*.tmp'`。         |
| `-P`             | 保持部分传输的文件并显示传输进度（等同于 `--partial --progress`）。 |
| `-e 命令`        | 指定远程 shell，例如 `-e "ssh -p 2222"` 指定 SSH 端口。      |

```bash
# 1. 本地目录同步
rsync -avz /source/ /destination/  						# 将/source/的内容同步到/destination/

# 2. 远程到本地同步
rsync -avz user@example.com:/remote/path/ /local/path/  # 通过SSH下载

# 3. 本地到远程同步
rsync -avz /local/path/ user@example.com:/remote/path/  # 通过SSH上传

# 4. 使用特定 SSH 端口
rsync -avz -e "ssh -p 2222" /local/ user@example.com:/remote/

# 5. 排除特定文件或目录
rsync -avz --exclude '*.log' --exclude 'temp/' /source/ /destination/

# 6. 断点续传
rsync -avzP /local/bigfile.zip user@example.com:/remote/  # -P选项支持断点续传

# 7. 删除目标端多余文件（强制完全同步）
rsync -avz --delete /source/ /destination/  # 删除目标端中不存在于源端的文件
```



## 系统监控与信息

### uname -a 

`uname -a` 是 Linux/Unix 系统中一个常用的命令，用于查看当前系统的内核版本、硬件架构、主机名等详细信息。其输出内容包含了系统的关键标识信息，对系统管理和问题排查非常有用。uptime 显示系统运行时间和负载

```bash
uname -a

Linux example-host 5.15.0-78-generic #85-Ubuntu SMP Fri Jul 14 17:33:24 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
```

| 字段顺序 | 含义说明                                                     |
| -------- | ------------------------------------------------------------ |
| 1        | **内核名称**（如 `Linux`、`Darwin` 或 `FreeBSD`，区分操作系统类型）。 |
| 2        | **主机名**（当前系统的主机名，可通过 `hostname` 命令单独查看）。 |
| 3        | **内核版本**（包含内核主版本、次版本、修订号及发行版特定标识，如 `5.15.0-78-generic`）。 |
| 4        | **内核编译信息**（如 `#85-Ubuntu SMP`，表示编译版本和发行版标识，`SMP` 表示支持多处理器）。 |
| 5        | **编译时间**（内核编译的时间和时区，如 `Fri Jul 14 17:33:24 UTC 2023`）。 |
| 6        | **硬件架构**（如 `x86_64` 表示 64 位 Intel/AMD 架构，`aarch64` 表示 64 位 ARM 架构）。 |
| 7        | **处理器架构**（通常与硬件架构一致，部分系统可能显示具体 CPU 类型）。 |
| 8        | **操作系统名称**（如 `GNU/Linux` 表示基于 GNU 工具链的 Linux 系统）。 |

**示例输出解读**

以输出 `Linux example-host 5.15.0-78-generic #85-Ubuntu SMP Fri Jul 14 17:33:24 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux` 为例：

- 系统是 **Linux** 内核。
- 主机名是 `example-host`。
- 内核版本是 `5.15.0-78-generic`（Ubuntu 发行版的定制内核）。
- 内核编译于 `2023年7月14日`。
- 硬件架构为 `x86_64`（64 位 Intel/AMD 处理器）。



### history 

`history`  查看之前的操作信息与编号

`history -c`  清空历史操作信息

### export 

`export` 是一个重要的 Shell 命令，用于设置和管理环境变量。环境变量是系统中存储的全局值，可供所有进程访问，常用于配置系统路径、语言设置、应用程序参数等。

```bash
export [变量名]=[值]  # 设置并导出变量
export [变量名]      # 导出已存在的变量
```

**核心功能**

1. **设置环境变量**：将变量的值传递给子进程。
2. **变量持久化**：使变量在当前 Shell 会话及其子进程中有效。
3. **配置系统参数**：如 `PATH`、`LANG`、`HOME` 等系统级变量。

```bash
# 1. 临时设置环境变量
export NAME="John"  # 设置变量NAME并导出
echo $NAME          # 输出：John

# 在子进程中访问
bash                # 启动新的子Shell
echo $NAME          # 输出：John（继承父Shell的环境变量）
exit                # 退出子Shell

# 2. 修改系统路径（PATH 变量）
export PATH=$PATH:/home/user/bin  # 将/home/user/bin添加到PATH
echo $PATH                        # 查看当前PATH

# 3. 设置语言环境
export LANG=en_US.UTF-8  # 设置语言为英文（UTF-8编码）

# 4. 导出已存在的变量
USER="admin"        # 定义普通变量
export USER         # 将USER导出为环境变量
```

| 特性     | 环境变量                                         | 普通变量          |
| -------- | ------------------------------------------------ | ----------------- |
| 作用域   | 当前 Shell 及其所有子进程                        | 仅当前 Shell 有效 |
| 访问方式 | 子进程可通过 `$变量名` 访问                      | 子进程无法访问    |
| 设置方式 | `export 变量名=值` 或 `变量名=值; export 变量名` | `变量名=值`       |
| 示例     | `export PATH=$PATH:/new/dir`                     | `count=10`        |

### env 

查看环境变量

```bash
# 1.直接
env

# 2.通过 env 可以为单个命令临时设置或修改环境变量，不影响当前 Shell 的全局环境。
# 临时修改LANG变量，以中文显示日期（仅对当前date命令有效）
env LANG=zh_CN.UTF-8 date
# 输出：2025年 07月12日 星期六 15:30:00 CST

# 临时添加路径到PATH，执行自定义脚本（无需永久修改PATH）
env PATH=$PATH:/home/john/scripts myscript.sh

# 3.使用 -i 选项可以清除所有现有环境变量，仅保留指定的变量执行命令：
# 清除所有环境变量，仅保留PATH和USER，然后执行echo
env -i PATH=$PATH USER=test echo "当前用户：$USER，路径：$PATH"
# 输出：当前用户：test，路径：/usr/local/sbin:/usr/local/bin:...

# 4.通过 -u 选项可以移除指定的环境变量，再执行命令：
# 先查看当前PATH
echo $PATH  # 假设输出包含 /home/john/temp

# 临时移除PATH中的/home/john/temp，执行命令
env -u PATH PATH=$(echo $PATH | sed 's#/home/john/temp:##') which myapp
# 此时which不会在被移除的路径中查找myapp
```

| 选项                           | 作用                                               |
| ------------------------------ | -------------------------------------------------- |
| `-i` 或 `--ignore-environment` | 忽略当前环境变量，创建一个空环境，再添加指定的变量 |
| `-u` 或 `--unset=变量名`       | 从环境中删除指定的变量                             |
| `--help`                       | 显示帮助信息                                       |
| `--version`                    | 显示版本信息                                       |

### reboot 

重新启动系统

### shutdown 

关闭系统



## vi/vim 

进入和退出

i命令：在当前光标处开始书写
a命令：在当前光标后开始书写
o命令：在当前光标的下一行插入一个新行开始书写
O命令：在当前光标的上一行插入一个新行开始书写
x命令：删除光标处的一个字符⚠️x命令之后依然处于命令模式

光标移动命令
hjkl这是4个命令，用来代表方向键（不考试）
0：快速定位到一行的开头
$：快速定位到行结束
G：定位到指定的行，单独的G一般都是到文件最后一行
    nG: n是一个数字，直接按，之后再按G，可以直接定位到文档的某一行
w：以单词为单位向后跳
b：以单词为单位向前跳

复制、粘贴
D：清空内容
dd: 剪切（也可以当删除用）
p和P：p是在当前行下一行粘贴，P是在当前行上一行粘贴
yy：复制
x: 删除光标所在位置的字符
X: 删除光标之前的字符
u: 撤销上一次操作，一直按一直撤销
ctrl+r：恢复

:set number显示行号


查找
/：/后面紧跟要查找的内容，比如/abc
?: 和/一样，只不过默认向前找
n：向后找
N：向前找

替换：
找到后直接替换
:s/旧内容/新内容   替换光标所在行的第一个内容
%的使用



## 输入/输出重定向

```bash
指令 > 文件名 			# 将内容存到文件中，覆盖
指令 >> 文件名 			# 将内容存到文件中，续写
```





## ubuntu的apt/snap命令

apt/snap命令一般都需要加sudo做前缀

1. sudo apt update  检查ubuntu中有哪些需要更新的内容

2. sudo apt upgrade  实际进行更新操作

3. sudo apt install 软件名   安装某一个软件

4. sudo apt remove 软件名     删除某一个软件

5. sudo apt search 软件名    查找是否安装了某个软件

6. apt list --installed    列出所有已经安装的软件





## shell的概念，比如bash，zsh，sh等什么是脚本，脚本的运行

**Shell** 是用户与操作系统内核之间的接口，负责解释执行用户输入的命令。而 **脚本** 则是一系列 Shell 命令的集合，用于自动化执行任务。

**Shell 的核心作用**

- 解释用户输入的命令并传递给内核执行。
- 提供命令行编辑、历史记录、别名等增强功能。
- 支持脚本编程，实现复杂任务的自动化。

| Shell 名称 | 特点                                                         | 默认系统                     |
| ---------- | ------------------------------------------------------------ | ---------------------------- |
| **sh**     | Bourne Shell（最古老的 Shell），遵循 POSIX 标准，兼容性强。  | 部分轻量级系统（如 BusyBox） |
| **bash**   | Bourne Again Shell，sh 的增强版，支持更多特性（如命令补全、数组）。 | 大多数 Linux 发行版          |
| **zsh**    | 功能丰富，支持高级自动补全、主题、插件系统（如 Oh My Zsh）。 | macOS（默认自 Catalina）     |
| **ksh**    | Korn Shell，商业 UNIX 系统常用，支持高级脚本编程。           | 部分 UNIX 系统               |
| **fish**   | Friendly Interactive Shell，交互式体验优秀，但脚本兼容性较差。 | 适合日常使用                 |

- 脚本是一个包含 Shell 命令的文本文件，通常以 `.sh` 为扩展名（非强制）。
- 第一行需指定解释器（Shebang），例如：`#!/bin/bash`。

**脚本的运行方式**

```bash
# 1. 作为可执行文件运行
	# 1. 赋予执行权限
chmod +x script.sh

	# 2. 执行脚本
./script.sh  # 当前目录下的脚本需加./

# 2. 通过 Shell 解释器运行
bash script.sh  # 无需执行权限
sh script.sh    # 使用sh解释器执行（兼容性更强）

# 3. 在当前 Shell 中执行（source 命令）
source script.sh  # 或 . script.sh
```

