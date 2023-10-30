[toc]

## 1 概述

sed是stream editor的简称，也就是流编辑器。它一次处理一行内容，处理时，把当前处理的行存储在临时缓冲区中，称为"pattern space"，接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有 改变，除非你使用重定向存储输出。

## 2 语法

### 2.1 命令格式

```shell
sed [option] 'command' input_file
```

### 2.2 常用选项

- **-n** 使用安静silent模式。在一般sed的用法中，所有来自stdin的内容一般都会被列出到屏幕上。但如果加上-n参数后，则只有经过sed特殊处理的那一行(或者动作)才会被列出来；

- **-e** 直接在指令列模式上进行 sed 的动作编辑；

- **-f** 直接将 sed 的动作写在一个文件内，-f filename则可以执行filename内的sed命令；

- **-r** 让sed命令支持扩展的正则表达式(默认是基础正则表达式)；

- **-i** 直接修改读取的文件内容，而不是由屏幕输出。

### 2.3 常用命令

- **a\\** 追加行，a\的后面跟上字符串s(多行字符串可以用\n分隔)，则会在当前选择的行的后面都加上字符串s；

- **c\\** 替换行，c\后面跟上字符串s(多行字符串可以用\n分隔)，则会将当前选中的行替换成字符串s；

- **i\\** 插入行，i\后面跟上字符串s(多行字符串可以用\n分隔)，则会在当前选中的行的前面都插入字符串s；

- **d** 删除行delete，该命令会将当前选中的行删除；

- **p** 打印print，该命令会打印当前选择的行到屏幕上；

- **y** 替换字符，通常y命令的用法是这样的：y/source-chars/sest-chars/，分割字符/可以用任意单字符代替，用dest-chars中对应位置的字符替换掉soutce-chars中对应位置的字符；

- **s** 替换字符串，通常s命令的用法是这样的：1,$s/regexp/replacement/Flags，分隔字符/可以用其他任意单字符代替，用Replacement替换掉匹配字符串。

### 2.4 替换选项

- **\digit** Replacement中可含有后向引用中的\digit(digit是1至9)，引用前面定义的子表达；

- **&** 代表模版空间中的整个匹配部分；

- **\L** 将在其后的替换部分转换成小写字母，直到发现一个\U或\E，GNU扩展功能；

- **\l** 将下一个字符转换成小写字母，GNU扩展功能；

- **\U** 将在其后的替换部分转换成大写字母，直到发现一个\L或\E，GNU扩展功能；

- **\u** 将下一个字符转换成大写字母，GNU扩展功能；

- **\E** 停止由\L或\U指示开始的大小写转换，GNU扩展功能。

### 2.5 标志选项

- **g** 将用Replacement替换模版空间中所有匹配Regexp的部分，则不仅仅是第一个匹配部分；

- **digit** 只用Replacement替换模版空间中第digit(digit是1至9)个匹配Regexp的部分；

- **p** 若发生了替换操作，指示显示模版空间中新的数据；

- **w file-name** 若发生了替换操作，指示将模版空间中新的数据写入指定的文件file-name中；

- **i** 表示进行Regexp匹配时，是不区分大小写字母的。

## 3 示例

```shell
$ cat test.txt
first line
second is longer
third is not last one
fourth is short
fifth is the last
```

### 3.1 删除

1. 删除2至3行的数据，并输出到终端。

```shell
$ sed '2,3d' test.txt
first line
fourth is short
fifth is the last
```

2. 只删除第2行。

```shell
$ sed '2d' test.txt
first line
third is not last one
fourth is short
fifth is the last
```

3. 删除第3行到最后一行。

```shell
$ sed '3,$d' test.txt
```

4. 删除包含"short"的行。

```shell
$ sed '/short/d' test.txt
first line
second is longer
third is not last one
fifth is the las
```

### 3.2 添加

1. 在第2行之后加入字符串"add new words"。

```shell
$ sed '2a\add new words' test.txt
first line
second is longer
add new words
third is not last one
fourth is short
fifth is the last
```

2. 在第2行之前加入字符串"add new words"。

```shell
$ sed '2i\add new words' test.txt
first line
add new words
second is longer
third is not last one
fourth is short
fifth is the last
```

3. 在第2行之前增加多行字符串，使用\n。

```shell
$ sed '2i\add new words\nbelow words' test.txt
first line
add new words
below words
second is longer
third is not last one
fourth is short
fifth is the last
```

### 3.3 显示

1. 显示2至4行的数据。

```shell
$ sed -n '2,4p' test.txt
second is longer
third is not last one
fourth is short
```

2. 显示first打头的行。

```shell
$ sed -n '/^first/p' test.txt
first line
```

3. 显示有"short"的行。

```shell
$ sed -n '/short/p' test.txt
fourth is short
```

### 3.4 替换

1. 把第2行之后替换为"replace words"。

```shell
$ sed '2,$c\replace words' test.txt
first line
replace words
```

2. 把包含"first"的的行替换为"replace words"。

``` shell
$ sed '/first/c\replace words' test.txt
replace words
second is longer
third is not last one
fourth is short
fifth is the last
```

3. 将"first"字母替换为"replace words"。

```shell
$ sed 's/first/replace words/g' test.txt
replace words line
second is longer
third is not last one
fourth is short
fifth is the last
```

4. 查出以"f"开头以"t"结尾的所有行，把"is"替换为"was"。

```shell
$ sed '/^f.*t$/s/is/was/g' test.txt
first line
second is longer
third is not last one
fourth was short
fifth was the last
```

5. 去掉所有行的"is "，保留第1个和第2个位置的字符。

```shell
$ sed 's/\(.*\)is \(.*\)/\1\2/g' test.txt
first line
second longer
third not last one
fourth short
fifth the last
```

### 3.5 多命令

删除第2和第3行，在第1行后添加"add new words"。

```shell
$ sed -e '2,3d' -e '1a\add new words' test.txt
first line
add new words
fourth is short
fifth is the last
```

### 3.6 直接修改文件

把所有以"er"结尾的行替换为"!"结尾，直接修改文件。

```shell
$ sed -i 's/\er$/\!/g' test.txt
first line
second is long!
third is not last one
fourth is short
fifth is the last
```