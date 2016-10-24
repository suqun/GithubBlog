---
title: Shell编程之正则表达式
date: 2016-01-13 21:38:39
tags:
    - Shell
    - Linux
toc: true
---

### 正则表达式与通配符
Linux中的正则表达式和通配符有些区别，正则表达式用来在文件中匹配符合条件的`字符串`，正则是`包含匹配`。grep，awk，sed等命令可以支持正则表达式。通配符用来匹配符合条件的`文件名`，通配符是`完全匹配`。ls，find，cp这些命令不支持正则所有只能用shell自己的通配符进行匹配。

<!-- more -->

通配符：

```bash
    *   匹配任意内容
    ？   匹配任意一个内容
    []  匹配中括号中的一个字符
```

### 基础正则表达式

Linux下基础正则表达式有：

```bash
    *   前一个字符匹配0次或任意多次
    .   匹配除了换行符外任意一个字符
    ^   匹配行首。例如：^hello会匹配以hello开头的行
    $   匹配行尾。例如：hello$会匹配以hello结尾的行
    []  匹配括号中指定的任意一个字符，只匹配一个字符。例如：[aoeiu]匹配任意一个元音字母，[0-9]匹配任意一个数字，[a-z][0-9]匹配任意一个字母和一位数字组成的2位字符
    [^] 匹配除括号中的字符以外的任意一个字符。例如：[^0-9]匹配任意一个非数字的字符，[^a-z]匹配任意一个非小写字母的字符
    \   转义字符
    \{n\}   表示其前面的字符恰好出现n次。例如：a[0-9]\{4\}b 匹配ab之间为4位数字的字符，注意前后加定界符
    \{n,\}  表示其前面的字符出现不小于n次。例如：[0-9]\{2\}表示2位及以上的数字，注意前后加定界符
    \{n,m\} 表示其前面的字符至少出现n次，至多出现m次。例如：[a-z]\{6,8\}匹配6到8位的小写字母
```

### 字符操作命令

#### cut

```bash
    名称
     cut -- 截取文件中每行的选定部分
     cut  [-bn] [file] 或 cut [-c] [file]  或  cut [-df] [file]
    说明
        -b ：以字节为单位进行分割。这些字节位置将忽略多字节字符边界，除非也指定了 -n 标志。
        -c ：以字符为单位进行分割。
        -d ：自定义分隔符，默认为制表符。
        -f ：与-d一起使用，指定显示哪个区域。
        -n ：取消分割多字节字符。仅和 -b 标志一起使用。如果字符的最后一个字节落在由 -b 标志的 List 参数指示的<br />范围之内，该字符将被写出；否则，该字符将被排除
```

我们将系统磁盘信息导出到文本/notes/linux/df中，来练习cut的使用

```bash
    ➜  linux  touch df
    ➜  linux  df -h > df
    ➜  linux  cat df
    Filesystem      Size   Used  Avail Capacity  iused    ifree %iused  Mounted on
    /dev/disk1     233Gi   50Gi  182Gi    22% 13178033 47803213   22%   /
    devfs          180Ki  180Ki    0Bi   100%      624        0  100%   /dev
    map -hosts       0Bi    0Bi    0Bi   100%        0        0  100%   /net
    map auto_home    0Bi    0Bi    0Bi   100%        0        0  100%   /home
```

我们首先截取df的第一列Filesystem和第五列Capacity

```bash
    ➜  linux  cut -f 1,5 df     <== -f 按列截取 1,5截取第一列和第5列
    Filesystem       Size   Used  Avail Capacity  iused    ifree %iused  Mounted on
    /dev/disk1     233Gi   50Gi  182Gi    22% 13178033 47803213   22%   /
    devfs          180Ki  180Ki    0Bi   100%      624        0  100%   /dev
    map -hosts       0Bi    0Bi    0Bi   100%        0        0  100%   /net
    map auto_home    0Bi    0Bi    0Bi   100%        0        0  100%   /home
```

结果显示，输出的和原来的一样，并没有将第一五两列截取出来。原因是`cut截取默认使用的分隔符是制表符`，而df文本中间的分隔符为空格，我们重新修改下命令，指定分隔符为空格

```bash
    ➜  linux  cut -f 1，5 -d " " df  <== -d 指定截取的分隔符，只能设置一个空格不能多个
    Filesystem
    /dev/disk1
    devfs
    map
    map
    ➜  linux  cut -f 1-8 -d " " df  <==截取第一列到第八列的
    Filesystem      Size
    /dev/disk1     233Gi
    devfs
    map -hosts
    map auto_home    0Bi
```

截取1，5两列时好像只截取了1列。原来cut指定的分隔符为空格，它就把一个空格作为分隔符，而df文本第一列后面是很多空格的，按照空格分隔的话，第五列是空格。cut截取命令比较简单，复杂的截取命令可以使用awk。

#### printf

```bash
    名称
     printf -- 格式化并输出结果到标准输出
     printf 格式替代符转义序列 内容 
    说明
        格式替代符 
        %b 相对应的参数被视为含有要被处理的转义序列之字符串。 
        %c ASCII字符。显示相对应参数的第一个字符 
        %d, %i 十进制整数 
        %e, %E, %f 浮点格式 
        %g %e或%f转换，看哪一个较短，则删除结尾的零 
        %G %E或%f转换，看哪一个较短，则删除结尾的零 
        %o 不带正负号的八进制值 
        %s 字符串 
        %u 不带正负号的十进制值 
        %x 不带正负号的十六进制值，使用a至f表示10至15 
        %X 不带正负号的十六进制值，使用A至F表示10至15 
        %% 字面意义的% 

        转义序列 
        \a 警告字符，通常为ASCII的BEL字符 
        \b 后退 
        \c 抑制（不显示）输出结果中任何结尾的换行字符（只在%b格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略 
        \f 换页（formfeed） 
        \n 换行 
        \r 回车（Carriage return） 
        \t 水平制表符 
        \v 垂直制表符 
        \\ 一个字面上的反斜杠字符 
        \ddd 表示1到3位数八进制值的字符，仅在格式字符串中有效 
        \0ddd 表示1到3位的八进制值字符
```

我们使用printf输出/notes/linux/df的内容

```bash
➜  linux  printf %s $(cat df)   <== printf不支持管道符`|`，使用$()的方式调用系统命令
FilesystemSizeUsedAvailCapacityiusedifree%iusedMountedon/dev/disk1233Gi50Gi182Gi22%131780334780321322%/devfs180Ki180Ki0Bi100%6240100%/devmap-hosts0Bi0Bi0Bi100%00100%/netmapauto_home0Bi0Bi0Bi100%00100%/home
```

可以看到，printf将df内容以字符串的形式直接输出,下面看下根据输出格式输出简单文本

```bash
# %-5s 格式为左对齐且宽度为5的字符串代替（-表示左对齐），不使用则是又对齐
# %-4.2f 格式为左对齐宽度为4，保留两位小数
➜  linux  printf "%-5s %-10s %-4s\n" NO Name Mark
   NO       Name Mark
➜  linux  printf "%-5s %-10s %-4.2f\n" 01 Tom 90.3456
   01        Tom 90.35
➜  linux  printf "%-5s %-10s %-4.2f\n" 02 Jack 89.2345
   02       Jack 89.23
```

#### awk

```bash
NAME
       awk - pattern-directed scanning and processing language

SYNOPSIS
       awk [ -F fs ] [ -v var=value ] [ 'prog' | -f progfile ] [ file ...  ]


DESCRIPTION

```

```
       Awk  scans  each input file for lines that match any of a set of patterns specified literally in prog or in one or more files specified as -f progfile.  With each pattern there
       can be an associated action that will be performed when a line of a file matches the pattern.  Each line is matched against the pattern portion of every  pattern-action  state-
       ment; the associated action is performed for each matched pattern.  The file name - means the standard input.  Any file of the form var=value is treated as an assignment, not a
       filename, and is executed at the time it would have been opened if it were a filename.  The option -v followed by var=value is an assignment to be done before prog is executed;
       any number of -v options may be present.  The -F fs option defines the input field separator to be the regular expression fs.

       An  input line is normally made up of fields separated by white space, or by regular expression FS.  The fields are denoted $1, $2, ..., while $0 refers to the entire line.  If
       FS is null, the input line is split into one field per character.
 

       A pattern-action statement has the form

              pattern { action }

       A missing { action } means print the line; a missing pattern always matches.  Pattern-action statements are separated by newlines or semicolons.

       An action is a sequence of statements.  A statement can be one of the following:

              if( expression ) statement [ else statement ]
              while( expression ) statement
              for( expression ; expression ; expression ) statement
              for( var in array ) statement
              do statement while( expression )
              break
              continue
              { [ statement ... ] }
              expression              # commonly var = expression
              print [ expression-list ] [ > expression ]
              printf format [ , expression-list ] [ > expression ]
              return [ expression ]
              next                    # skip remaining patterns on this input line
              nextfile                # skip rest of this file, open next, start at top
              delete array[ expression ] # delete an array element
              delete array            # delete all elements of array
              exit [ expression ]     # exit immediately; status is expression
```

#### sed



#### sort

```bash
NAME
       sort - 对文件行进行排序
SYNOPSIS
       sort [OPTION]... [FILE]...
DESCRIPTION
       -f 忽略大小写
       -n 以数值型进行排序，默认字符串类型排序
       -r 反向排序
       -t 指定分隔符，默认分隔符为制表符
       -k POS1[,POS2] 按照指定的字段范围排序，从Pos1开始到Pos2结尾（默认到行尾）
```

做些练习，首先查看用户信息文件/etc/passwd,排序

```bash
➜  linux  sort /etc/passwd
...
_amavisd:*:83:83:AMaViS Daemon:/var/virusmails:/usr/bin/false
_appleevents:*:55:55:AppleEvents Daemon:/var/empty:/usr/bin/false
_appowner:*:87:87:Application Owner:/var/empty:/usr/bin/false
```

反向排序

```bash
➜  linux  sort -r /etc/passwd
root:*:0:0:System Administrator:/var/root:/bin/sh
nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
daemon:*:1:1:System Services:/var/root:/usr/bin/false
_xserverdocs:*:251:251:OS X Server Documents Service:/var/empty:/usr/bin/false
_wwwproxy:*:252:252:WWW Proxy:/var/empty:/usr/bin/false
_www:*:70:70:World Wide Web Server:/Library/WebServer:/usr/bin/false
_windowserver:*:88:88:WindowServer:/var/empty:/usr/bin/false
```

指定分隔符":",用第三个字段开头，第三个字段结尾排序，就是只使用第三个字段排序，主要下面的第三个字段，-2，0，1，13，249...26，27，31，32，4，54 按字符排序，若使用数值排序加 `-n`

```bash
➜  linux  sort -t ":" -k 3,3 /etc/passwd
nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
root:*:0:0:System Administrator:/var/root:/bin/sh
daemon:*:1:1:System Services:/var/root:/usr/bin/false
_taskgated:*:13:13:Task Gate Daemon:/var/empty:/usr/bin/false
_ondemand:*:249:249:On Demand Resource Daemon:/var/db/ondemand:/usr/bin/false
_installassistant:*:25:25:Install Assistant:/var/empty:/usr/bin/false
_xserverdocs:*:251:251:OS X Server Documents Service:/var/empty:/usr/bin/false
_wwwproxy:*:252:252:WWW Proxy:/var/empty:/usr/bin/false
_lp:*:26:26:Printing Services:/var/spool/cups:/usr/bin/false
_postfix:*:27:27:Postfix Mail Server:/var/spool/postfix:/usr/bin/false
_scsd:*:31:31:Service Configuration Service:/var/empty:/usr/bin/false
_ces:*:32:32:Certificate Enrollment Service:/var/empty:/usr/bin/false
_uucp:*:4:4:Unix to Unix Copy Protocol:/var/spool/uucp:/usr/sbin/uucico
_mcxalr:*:54:54:MCX AppLaunch:/var/empty:/usr/bin/false

#第三个字段按数值排序 -2,0,1,4,13,24...
➜  linux  sort -n -t ":" -k 3,3 /etc/passwd
nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
root:*:0:0:System Administrator:/var/root:/bin/sh
daemon:*:1:1:System Services:/var/root:/usr/bin/false
_uucp:*:4:4:Unix to Unix Copy Protocol:/var/spool/uucp:/usr/sbin/uucico
_taskgated:*:13:13:Task Gate Daemon:/var/empty:/usr/bin/false
_networkd:*:24:24:Network Services:/var/networkd:/usr/bin/false
```

#### wc

```bash
NAME
     wc -- 统计字数，行数，字符数，字节数

SYNOPSIS
     wc [-clmw] [file ...]

DESCRIPTION
     -c      The number of bytes in each input file is written to the standard output.  This will cancel out any prior usage of the -m option.
     -l      The number of lines in each input file is written to the standard output.
     -m      The number of characters in each input file is written to the standard output.  If the current locale does not support multibyte characters, this is equivalent to the -c
             option.  This will cancel out any prior usage of the -c option.
     -w      The number of words in each input file is written to the standard output.
```

查看用户信息的行数

```bash
➜  linux  wc -l /etc/passwd
      96 /etc/passwd
```























参考资料: 

[http://www.imooc.com/learn/378](http://www.imooc.com/learn/378)

[http://man.linuxde.net/printf](http://man.linuxde.net/printf)
