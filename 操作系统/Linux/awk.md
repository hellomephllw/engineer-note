[toc]

## 1 简介

awk是一个强大的文本分析工具，相对于grep的查找，sed的编辑，awk在其对数据分析并生成报告时，显得尤为强大。简单来说awk就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理。

awk有3个不同版本: awk、nawk和gawk，未作特别说明，一般指gawk，gawk 是 AWK 的 GNU 版本。

awk其名称得自于它的创始人 Alfred Aho 、Peter Weinberger 和 Brian Kernighan 姓氏的首个字母。实际上 AWK 的确拥有自己的语言： AWK 程序设计语言，三位创建者已将它正式定义为“样式扫描和处理语言”。它允许您创建简短的程序，这些程序读取输入文件、为数据排序、处理数据、对输入执行计算以及生成报表，还有无数其他的功能。

## 2 语法

### 2.1 语法结构

一个awk脚本通常由：BEGIN语句块、能够使用模式匹配的通用语句块、END语句块3部分组成，这三个部分是可选的。任意一个部分都可以不出现在脚本中，脚本通常是被单引号中。

```
awk 'BEGIN { commands } pattern { commands } END { commands }'
```

#### 2.1.1 执行步骤

- 第一步：执行BEGIN { commands }语句块中的语句；
- 第二步：从文件或标准输入(stdin)读取一行，然后执行pattern{ commands }语句块，它逐行扫描文件，从第一行到最后一行重复这个过程，直到文件全部被读取完毕。
- 第三步：当读至输入流末尾时，执行END { commands }语句块。

#### 2.1.2 执行原理

- BEGIN语句块：在awk开始从输入流中读取行之前被执行，这是一个可选的语句块，比如变量初始化、打印输出表格的表头等语句通常可以写在BEGIN语句块中。
- END语句块：在awk从输入流中读取完所有的行之后即被执行，比如打印所有行的分析结果这类信息汇总都是在END语句块中完成，它也是一个可选语句块。
- pattern语句块：中的通用命令是最重要的部分，它也是可选的。如果没有提供pattern语句块，则默认执行{ print }，即打印每一个读取到的行，awk读取的每一行都会执行该语句块。
- 域：读入有“\n”换行符分割的一条记录，然后将记录按指定的域分隔符划分域，填充域，$0则表示所有域，$1表示第一个域，$n表示第n个域。默认域分隔符是“空格”或“\t”。

### 2.2 示例

```
$ last -n 5
ezops    pts/0        172.27.1.3       Sun Oct 22 17:03   still logged in   
ezops    pts/0        172.27.1.3       Thu Oct 19 18:32 - 19:32  (01:00)    
ezops    pts/0        172.27.1.3       Thu Oct 19 17:01 - 17:46  (00:45)    
ezops    pts/0        172.27.1.3       Thu Oct 19 10:14 - 16:53  (06:39)    
ezops    pts/0        172.27.1.3       Wed Oct 18 13:32 - 00:49  (11:16)
```

```
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

- **示例1**

    如下示例显示最近5个登陆帐号，使用$1打印出第一个域。
    ```
    $ last -n 5 | awk '{print $1}'
    ezops
    ezops
    ezops
    ezops
    ezops
    ```

- **示例2**

    如下示例使用“-F ':'”指定分隔符为“:”，使用$1打印出第一个域。
    ```
    $ cat /etc/passwd | awk  -F ':' '{print $1}'
    root
    bin
    daemon
    adm
    lp
    ```

- **示例3**
    
    如下示例打印账户和账户对应的shell，使用双引号来拼接变量。
    ```
    $ cat /etc/passwd | awk  -F ':' '{print $1"\t"$7}'
    root    /bin/bash
    bin     /sbin/nologin
    daemon  /sbin/nologin
    adm     /sbin/nologin
    lp      /sbin/nologin
    ```

- **示例4**

    如下示例打印账户和账户对应的shell，在打印之前加入开始标识符和列名，在打印之后加入结束标识符。
    ```
    $ cat /etc/passwd | awk -F ':' 'BEGIN {print ">>>>>>>>>>\nname,shell"} {print $1","$7} END {print "<<<<<<<<<<"}'
    >>>>>>>>>>
    name,shell
    root,/bin/bash
    bin,/sbin/nologin
    daemon,/sbin/nologin
    adm,/sbin/nologin
    lp,/sbin/nologin
    <<<<<<<<<<
    ```

## 3 内置变量

```
ARGC               命令行参数个数
ARGV               命令行参数排列
ENVIRON            支持队列中系统环境变量的使用
FILENAME           awk浏览的文件名
FNR                浏览文件的记录数
FS                 设置输入域分隔符，等价于命令行 -F选项
NF                 浏览记录的域的个数
NR                 已读的记录数
OFS                输出域分隔符
ORS                输出记录分隔符
RS                 控制记录分隔符
```

### 3.1 示例

```
$ awk -F ':' '{print "filename:"FILENAME",linenumber:"NR",columns:"NF",linecontent:"$0}' /etc/passwd
filename:/etc/passwd,linenumber:1,columns:7,linecontent:root:x:0:0:root:/root:/bin/bash
filename:/etc/passwd,linenumber:2,columns:7,linecontent:bin:x:1:1:bin:/bin:/sbin/nologin
filename:/etc/passwd,linenumber:3,columns:7,linecontent:daemon:x:2:2:daemon:/sbin:/sbin/nologin
filename:/etc/passwd,linenumber:4,columns:7,linecontent:adm:x:3:4:adm:/var/adm:/sbin/nologin
filename:/etc/passwd,linenumber:5,columns:7,linecontent:lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

## 4 打印

### 4.1 print

print函数的参数可以是变量、数值或者字符串。字符串必须用双引号引用，参数用逗号分隔。如果没有逗号，参数就串联在一起而无法区分。这里，逗号的作用与输出文件的分隔符的作用是一样的，只是后者是空格而已。

例如：

```
$ awk -F ':' '{print $1,$7}' /etc/passwd
root /bin/bash
bin /sbin/nologin
daemon /sbin/nologin
adm /sbin/nologin
lp /sbin/nologin
```

### 4.2 printf

格式化字符串包括两部分内容：一部分是正常字符，这些字符将按原样输出; 另一部分是格式化规定字符，以"%"开始，后跟一个或几个规定字符,用来确定输出内容格式。

<table>
  <thead>
    <tr>
      <th>格式</th>
      <th>描述</th>
      <th>格式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>%d</td>
      <td>十进制有符号整数</td>
      <td>%u</td>
      <td>十进制无符号整数</td>
    </tr>
    <tr>
      <td>%f</td>
      <td>浮点数</td>
      <td>%s</td>
      <td>字符串</td>
    </tr>
    <tr>
      <td>%c</td>
      <td>单个字符</td>
      <td>%p</td>
      <td>指针的值</td>
    </tr>
    <tr>
      <td>%e</td>
      <td>指数形式的浮点数</td>
      <td>%x</td>
      <td>%X 无符号以十六进制表示的整数</td>
    </tr>
    <tr>
      <td>%o</td>
      <td>无符号以八进制表示的整数</td>
      <td>%g</td>
      <td>自动选择合适的表示法</td>
    </tr>
  </tbody>
</table>

例如：

```
awk -F ':' '{printf("filename:%15s,linenumber:%-5d,columns:%s,linecontent:%s\n",FILENAME,NR,NF,$0)}' /etc/passwd
filename:    /etc/passwd,linenumber:1    ,columns:7,linecontent:root:x:0:0:root:/root:/bin/bash
filename:    /etc/passwd,linenumber:2    ,columns:7,linecontent:bin:x:1:1:bin:/bin:/sbin/nologin
filename:    /etc/passwd,linenumber:3    ,columns:7,linecontent:daemon:x:2:2:daemon:/sbin:/sbin/nologin
filename:    /etc/passwd,linenumber:4    ,columns:7,linecontent:adm:x:3:4:adm:/var/adm:/sbin/nologin
filename:    /etc/passwd,linenumber:5    ,columns:7,linecontent:lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

其中，%15s表示如果不够15位，则在左侧补充空格字符，直到满足15位字符串。
而%-5d则表示如果不够5位，则在右侧补充空格字符，直到满足5位字符串。

## 5 编程

### 5.1 变量

例如，统计账户人数：

```
$ awk 'BEGIN {count=0;print "[start] user count is", count} {count=count+1;print $0;} END {print "[end] user count is", count}' /etc/passwd
[start] user count is 0
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
[end] user count is 5
```

例如，某个文件夹下的文件占用的大小（单位：M）

```
$ ls -l |awk 'BEGIN {size=0} {size=size+$5} END {print "[end] size is ", size/1024/1024,"M"}'
[end] size is  8.25889 M
```

### 5.2 条件语句

略

### 5.3 循环语句

略

### 5.4 数组

略

## 6 字符串函数

### 6.1 length

```
$ awk -F: '{print length($7)}' /etc/passwd
9
13
13
13
13
```
### 6.2 index

```
$ head -n 1 /etc/passwd | awk -F ':' '{print index($7,"bash")}'
6
```

### 6.3 sub

```
$ head -n 1 /etc/passwd | awk -F ':' '{sub("bash","test",$7)} END {print $7}'
/bin/test
```

### 6.4 substr

```
$ head -n 1 /etc/passwd | awk -F ':' '{print substr($7,0,5)}'
/bin/
```

### 6.5 大小写

```
$ head -n 1 /etc/passwd | awk -F ':' '{print toupper($7)}'
/BIN/BASH
```

```
$ head -n 1 /etc/passwd | awk -F ':' '{print tolower(toupper($7))}'
/bin/bash
```