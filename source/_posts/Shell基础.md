---
title: Shell基础
date: 2016-01-02 20:40:52
tags: 
    - Shell
    - Linux
toc: true
---

## Shell 概述

[imooc shell课程](http://www.imooc.com/learn/175)

Shell是一个命令行解释器，它为用户提供了一个向Linux内核发送请求以便运行程序的界面系统级程序，用可以用Shell来启动，挂起，停止甚至是编写一些程序。

Shell还是一个功能强大的编程语言，易编写易调试，灵活性较强。Shell是解释执行的脚本语言，在Shell中可以直接调用Linux系统命令。

<!-- more -->

echo $SHELL 查看当前计算机的shell版本，我用的是Mac的系统，所以查询出来的是zsh版本，Linux系统多是Bash版本

    ➜  ~  echo $SHELL
    /bin/zsh
    

在 /etc/shells文件中查看系统支持的shell版本

    ➜  /etc  vim /etc/shells

    # List of acceptable shells for chpass(1).
    # Ftpd will not allow users to connect who are not using
    # one of these shells.

    /bin/bash
    /bin/csh
    /bin/ksh
    /bin/sh
    /bin/tcsh
    /bin/zsh

可以使用shells文件中的兼容版本切换当前系统的shell版本

    ➜  /etc  bash
    bash-3.2$ exit
    exit
    ➜  /etc

## 脚本执行方式

### echo 输出命令

    echo [-e] [输出内容]

-e 若字符串中出现以下字符，则特别加以处理，而不会将它当成一般文字输出

![echo -e](http://7xpk5e.com1.z0.glb.clouddn.com/shell-echo-e.png)

### 第一个脚本

    bash-3.2$ vim hello.sh

        #!/bin/bash
        #The first program

        echo "Happy New Year"

    bash-3.2$ sh hello.sh
    Happy New Year
    bash-3.2$

- sh hello.sh 执行脚本
- chmod 755 hello.sh 然后直接 hello.sh执行脚本

## Bash的基本功能

### 命令别名

**alias 查看系统中所有的命令别名**

    ➜  shellnotes  alias

    history='fc -l 1'
    l='ls -lah'
    la='ls -lAh'
    ls='ls -G'
    lsa='ls -lah'
    md='mkdir -p'
    ...

**alias 别名='原命令' 设置别名命令**

    alias ll = 'ls -lh'

**unalias 取消别名**
    
    unalias ll

### 常用快捷键

删除

    ctrl + d      删除光标所在位置上的字符相当于VIM里x或者dl
    ctrl + h      删除光标所在位置前的字符相当于VIM里hx或者dh
    ctrl + k      删除光标后面所有字符相当于VIM里d shift+$
    ctrl + u      删除光标前面所有字符相当于VIM里d shift+^
    ctrl + w      删除光标前一个单词相当于VIM里db
    ctrl + y      恢复ctrl+u上次执行时删除的字符
    ctrl + ?      撤消前一次输入
    alt  + r      撤消前一次动作
    alt  + d     删除光标所在位置的后单词

移动

    ctrl + a      将光标移动到命令行开头相当于VIM里shift+^
    ctrl + e      将光标移动到命令行结尾处相当于VIM里shift+$
    ctrl + f      光标向后移动一个字符相当于VIM里l
    ctrl + b      光标向前移动一个字符相当于VIM里h
    ctrl + 方向键左键    光标移动到前一个单词开头
    ctrl + 方向键右键    光标移动到后一个单词结尾
    ctrl + x       在上次光标所在字符和当前光标所在字符之间跳转
    alt  + f      跳到光标所在位置单词尾部


替换

    ctrl + t       将光标当前字符与前面一个字符替换
    alt  + t     交换两个光标当前所处位置单词和光标前一个单词
    alt  + u     把光标当前位置单词变为大写
    alt  + l      把光标当前位置单词变为小写
    alt  + c      把光标当前位置单词头一个字母变为大写
    ^oldstr^newstr    替换前一次命令中字符串   

历史命令编辑

    ctrl + p   返回上一次输入命令字符
    ctrl + r       输入单词搜索历史命令
    alt  + p     输入字符查找与字符相接近的历史命令
    alt  + >     返回上一次执行命令

其它

    ctrl + s      锁住终端
    ctrl + q      解锁终端
    ctrl + l        清屏相当于命令clear
    ctrl + c       另起一行
    ctrl + i       类似TAB健补全功能
    ctrl + o      重复执行命令
    alt  + 数字键  操作的次数

[命令快捷键参考地址](http://rainbird.blog.51cto.com/211214/66031/)

### 历史命令

#### history

在预设的情况下，会将历史纪录写入~/.bash_history,默认保存1000条，可以在环境变量配置文件/etc/profile中修改HISTSIZE的值
    
    [dmtsai@study ~]$ history [n] 
    [dmtsai@study ~]$ history [-c] 
    [dmtsai@study ~]$ history [-raw] histfiles 
    选项与参数：
    n ：数字，意思是『要列出最近的n笔命令列表』的意思！
    -c ：将目前的shell中的所有history内容全部消除
    -a ：将目前新增的history指令新增入histfiles中，若没有加histfiles ，
          则预设写入~/.bash_history 
    -r ：将histfiles的内容读到目前这个shell的history记忆中；
    -w ：将目前的history记忆内容写入histfiles中！

    范例一：列出目前记忆体内的所有history记忆 
    [dmtsai@study ~]$ history 
    #前面省略 
     1017 man bash 
     1018 ll 
     1019 history 
     1020 history #列出的资讯当中，共分两栏，第一栏为该指令在这个shell当中的代码，
    #另一个则是指令本身的内容喔！至于会秀出几笔指令记录，则与HISTSIZE有关！

    范例二：列出目前最近的3笔资料 
    [dmtsai@study ~]$ history 3 
     1019 history 
     1020 history 
     1021 history 3 

    范例三：立刻将目前的资料写入histfile当中 
    [dmtsai@study ~]$ history -w #在预设的情况下，会将历史纪录写入~/.bash_history当中！
    [dmtsai@study ~]$ echo ${HISTSIZE} 
    1000 

[history参考鸟哥资料](http://linux.vbird.org/linux_basic/0320bash.php#alias_history)

#### 历史命令的调用

1. 使用『上下箭头』调用以前的历史命令
2. 使用『!n』重复执行第N条命令
3. 使用『!!』重复执行上一条命令
4. 使用『!字符串』重复执行最后一条以该字符串开头的命令

### 资料流重导向

资料流重导向就是将某个指令执行后应该要出现在萤幕上的资料， 给他传输到其他的地方

#### standard output 与standard error output

标准输出指的是『指令执行所回传的正确的信息』，而标准错误输出可理解为『指令执行失败后，所回传的错误信息』。举个例子：当前目录下存在Client.java而不存在Server.java，若执行命令"cat Client.java Server.java" , cat 会进行：

- 标准输出：读取Client.java，将文档内容显示在屏幕上
- 标准错误输出：因为无法找到Server.java，因此屏幕上显示错误信息

    ➜  Documents  cat Client.java Server.java
    public class Client {

    }
    cat: Server.java: No such file or directory
    ➜  Documents
        
资料流重导向可以将standard output (简称stdout) 与standard error output (简称stderr) 分别传送到其他的档案或装置去，而分别传送所用的特殊字元则如下所示：

1. 标准输入(stdin) ：代码为0 ，使用< 或<< ；
2. 标准输出(stdout)：代码为1 ，使用> 或>> ；
3. 标准错误输出(stderr)：代码为2 ，使用2> 或2>> ；

    范例一：观察目录(~)下各目录的档名、权限与属性，并记录下来 
    ➜  ~  ll     <==此时萤幕会显示出档名
    total 8
    drwx------   6 larry  staff   204B 11 24 23:18 Applications
    drwx------+ 25 larry  staff   850B  1  2 21:45 Desktop
    drwx------+ 22 larry  staff   748B  1  3 19:27 Documents
    drwx------+ 23 larry  staff   782B  1  3 18:33 Downloads
    drwxr-xr-x   4 larry  staff   136B  1  2 21:52 IdeaProjects
    drwx------@ 54 larry  staff   1.8K 12  6 09:45 Library
    drwx------+  3 larry  staff   102B 11 11 12:17 Movies
    drwx------+  4 larry  staff   136B 12  6 09:45 Music
    drwx------+  5 larry  staff   170B 12 13 22:02 Pictures
    drwxr-xr-x+  5 larry  staff   170B 11 11 12:17 Public
    -rw-r--r--   1 larry  staff    53B 11 30 23:02 dump.rdb
    ➜  ~  ll > rootfile  <==萤幕并无任何信息
    ➜  ~  ll rootfile    <==有个新档被建立了
    -rw-r--r--  1 larry  staff   681B  1  3 19:33 rootfile

该档案(本例中是rootfile) 若不存在，系统会自动的将他建立起来。当这个档案存在的时候，那么系统就会先将这个档案内容清空，然后再将资料写入。也就是若以> 输出到一个已存在的档案中，那个档案就会被覆盖掉。如果不想覆盖,使用『ll >> rootfile』在文件rootfile下方累加

- 1> ：以覆盖的方法将『正确的资料』输出到指定的档案或装置上；
- 1>>：以累加的方法将『正确的资料』输出到指定的档案或装置上；
- 2> ：以覆盖的方法将『错误的资料』输出到指定的档案或装置上；
- 2>>：以累加的方法将『错误的资料』输出到指定的档案或装置上；

    范例三：承范例二，将stdout与stderr分存到不同的档案去 
    [dmtsai@study ~]$ find /home -name .bashrc > list_right 2> list_error

    范例四：承范例三，将错误的资料丢弃，萤幕上显示正确的资料 
    [dmtsai@study ~]$ find /home -name .bashrc 2> /dev/null 
    /home/dmtsai/.bashrc   <==只有stdout会显示到萤幕上， stderr被丢弃了

    范例五：将指令的资料全部写入名为list的档案中 
    [dmtsai@study ~]$ find /home -name .bashrc > list 2> list   <==错误 
    [dmtsai@study ~]$ find /home - name .bashrc > list 2>&1      <==正确 
    [dmtsai@study ~]$ find /home -name .bashrc &> list          <==正确

写入同一个档案的特殊语法如上表所示，你可以使用2>&1也可以使用&> 

#### standard input ： < 与<<

将原本需要由键盘输入的资料，改由档案内容来取代

    范例一：利用cat指令来建立一个档案的简单流程 
    [dmtsai@study ~]$ cat > catfile 
    testing 
    cat file test 
    <==这里按下[ctrl]+d来离开
     
    [dmtsai@study ~]$ cat catfile 
    testing 
    cat file test

由于加入>在cat后，所以那个catfile会被主动的建立，而内容就是刚刚键盘上面输入的那两行资料。
下面用纯文字档取代键盘的输入，也就是说，用某个档案的内容来取代键盘的敲击

    范例二：用stdin取代键盘的输入以建立新档案的简单流程 
    [dmtsai@study ~]$ cat > catfile < ~/.bashrc 
    [dmtsai@study ~]$ ll catfile ~/.bashrc 
    -rw-r-- r--. 1 dmtsai dmtsai 231 Mar 6 06:06 /home/dmtsai/.bashrc 
    -rw-rw-r--. 1 dmtsai dmtsai 231 Jul 9 18:58 catfile
     #注意看，这两个档案的大小会一模一样！几乎像是使用cp来复制一般！


『<<』 这个连续两个小于的符号代表的是『结束的输入字元』的意思。举例来讲：『我要用cat 直接将输入的讯息输出到catfile 中， 且当由键盘输入eof 时，该次输入就结束』，那我可以这样做：

    [dmtsai@study ~]$ cat > catfile << "eof" 
    This is a test. 
    OK now stop 
    eof   <==输入这关键字，立刻就结束而不需要输入[ctrl]+d
     
    [dmtsai@ study ~]$ cat catfile 
    This is a test. 
    OK now stop      <==只有这两行，不会存在关键字那一行！

利用<< 右侧的控制字元，我们可以终止一次输入， 而不必输入[crtl]+d 来结束.

### 命令执行的判断依据 ; && ||

在某些情况下，很多指令我想要一次输入去执行，而不想要分次执行时，该如何是好？基本上你有两个选择，一个是透过shell script 撰写脚本去执行，一种则是透过底下的介绍来一次输入多重指令。

#### cmd ; cmd (不考虑指令相关性的连续指令下达)

在指令与指令中间利用分号(;) 来隔开，这样一来，分号前的指令执行完后就会立刻接着执行后面的指令了。    

#### $? (指令回传值) 与&& 或||

如同上面谈到的，两个指令之间有相依性，而这个相依性主要判断的地方就在于前一个指令执行的结果是否正确。

**若前一个指令执行的结果为正确，在Linux底下会回传一个$? = 0的值**

- cmd1 && cmd2
    + 若cmd1执行完毕且正确执行($?=0)，则开始执行cmd2。
    + 若cmd1执行完毕且为错误($?≠0)，则cmd2不执行。
- cmd1 || cmd2
    + 若cmd1执行完毕且正确执行($?=0)，则cmd2不执行。
    + 若cmd1执行完毕且为错误($?≠0)，则开始执行cmd2。

[鸟哥资料](http://linux.vbird.org/linux_basic/0320bash.php#redirect)


### 管线命令(pipe)

#### 管道符 | 

管线命令使用的是『 | 』这个符号.

我们来查询系统的网络连接情况 netstat -an

    ➜  ~ netstat -an
    Active Internet connections (including servers)
    Proto Recv-Q Send-Q  Local Address          Foreign Address        (state)
    tcp4       0      0  127.0.0.1.51004        127.0.0.1.50724        ESTABLISHED
    tcp4       0      0  127.0.0.1.50724        127.0.0.1.51004        ESTABLISHED
    tcp4       0      0  192.168.1.105.50722    106.187.89.142.443     ESTABLISHED
    ...很多很多页


内容太多导致屏幕被塞满了，我们通过less命令分页显示,ctrl+n下一页，ctrl+b上一页,如此一来，使用" netstat -an"指令输出后的内容，就能够被less读取，并且利用less的功能，我们就能够前后翻动相关的信息了。其实这个**管线命令『 | 』仅能处理经由前面一个指令传来的正确资讯，也就是standard output的资讯，对于stdandard error并没有直接处理的能力**。

    ➜  ~  netstat -an | less

在每个管线后面接的第一个资料必定是『指令』喔！而且**这个指令必须要能够接受standard input的资料**才行，这样的指令才可以是为『管线命令』，例如**less, more, head, tail**等都是可以接受standard input的管线命令啦。至于例如ls, cp, mv等就不是管线命令了！因为ls, cp, mv并不会接受来自stdin的资料。也就是说，管线命令主要有两个比较需要注意的地方：

1. 管线命令仅会处理standard output，对于standard error output 会予以忽略
2. 管线命令必须要能够接受来自前一个指令的资料成为standard input 继续处理才行。

#### 撷取命令： cut, grep

什么是撷取命令啊？说穿了，就是将一段资料经过分析后，取出我们所想要的。或者是经由分析关键字，取得我们所想要的那一行！不过，要注意的是，一般来说，撷取讯息通常是针对『一行一行』来分析的，并不是整篇讯息分析的喔～底下我们介绍两个很常用的讯息撷取命令：

##### cut

这个指令可以将一段信息的某一段给他『切』出来。 处理的信息是以『行』为单位。

    [dmtsai@study ~]$ cut -d'分隔字元' -f fields  <==用于有特定分隔字元 
    [dmtsai@study ~]$ cut -c字元区间             <==用于排列整齐的讯息
    选项与参数：
    -d ：后面接分隔字元。与-f一起使用；
    -f ：依据-d的分隔字元将一段讯息分割成为数段，用-f取出第几段的意思；
    -c ：以字元(characters)的单位取出固定字元区间；

    范例一：将PATH变数取出，我要找出第五个路径。
    [dmtsai@study ~]$ echo ${PATH} 
    /usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/dmtsai/.local/bin:/home/dmtsai /bin # 1 | 2 | 3 | 4 | 5 | 6 | 
    [dmtsai@study ~]$ echo ${PATH} | cut -d ':' -f 5 #如同上面的数字显示，我们是以『 : 』作为分隔，因此会出现/home/dmtsai/.local/bin 
    #那么如果想要列出第3与第5呢？，就是这样： 
    [dmtsai@study ~]$ echo ${PATH} | cut -d ':' -f 3,5 

    范例二：将export输出的讯息，取得第12字元以后的所有字串 
    [dmtsai@ study ~]$ export 
    declare -x HISTCONTROL="ignoredups" 
    declare -x HISTSIZE="1000" 
    declare -x HOME="/home/dmtsai" 
    declare -x HOSTNAME="study.centos.vbird" .....(其他省略)..... 
    #注意看，每个资料都是排列整齐的输出！如果我们不想要『 declare -x 』时，就得这么做： 
    [dmtsai@study ~]$ export | cut -c 12- 
    HISTCONTROL="ignoredups" 
    HISTSIZE="1000" 
    HOME="/home/dmtsai" 
    HOSTNAME= "study.centos.vbird" .....(其他省略)..... 
    #知道怎么回事了吧？用-c可以处理比较具有格式的输出资料！
    #我们还可以指定某个范围的值，例如第12-20的字元，就是cut -c 12-20等等！

    范例三：用last将显示的登入者的资讯中，仅留下使用者大名 
    [dmtsai@study ~]$ last 
    root pts/1 192.168.201.101 Sat Feb 7 12:35 still logged in 
    root pts/1 192.168. 201.101 Fri Feb 6 12:13 - 18:46 (06:33) 
    root pts/1 192.168.201.254 Thu Feb 5 22:37 - 23:53 (01:16) # last可以输出『帐号/终端机/来源/日期时间』的资料，并且是排列整齐的 
    [dmtsai@study ~]$ last | cut -d ' ' -f 1 #由输出的结果我们可以发现第一个空白分隔的栏位代表帐号，所以使用如上指令：
    #但是因为root pts/1之间空格有好几个，并非仅有一个，所以，如果要找出
    # pts/1其实不能以cut -d ' ' -f 1,2喔！输出的结果会不是我们想要的。

##### grep

刚刚的cut 是将一行讯息当中，取出某部分我们想要的，而grep 则是分析一行讯息， 若当中有我们所需要的资讯，就将该行拿出来～简单的语法是这样的：

    [dmtsai@study ~]$ grep [-acinv] [--color=auto] '搜寻字串' filename 
    选项与参数：
    -a ：将binary档案以text档案的方式搜寻资料
    -c ：计算找到'搜寻字串'的次数
    -i ：忽略大小写的不同，所以大小写视为相同
    -n ：顺便输出行号
    -v ：反向选择，亦即显示出没有'搜寻字串'内容的那一行！
    --color=auto ：可以将找到的关键字部分加上颜色的显示喔！

    范例一：将last当中，有出现root的那一行就取出来； 
    [dmtsai@study ~]$ last | grep 'root' 

    范例二：与范例一相反，只要没有root的就取出！
    [dmtsai@study ~]$ last | grep -v 'root' 

    范例三：在last的输出讯息中，只要有root就取出，并且仅取第一栏 
    [dmtsai@study ~]$ last | grep 'root' |cut -d ' ' -f1 
    #在取出root之后，利用上个指令cut的处理，就能够仅取得第一栏啰！

    范例四：取出/etc/man_db.conf内含MANPATH的那几行 
    [dmtsai@study ~]$ grep --color=auto 'MANPATH' /etc/man_db.conf 
    ....(前面省略)... . 
    MANPATH _MAP /usr/games /usr/share/man
     MANPATH _MAP /opt/bin /opt/man
     MANPATH _MAP /opt/sbin /opt/man
     #神奇的是，如果加上--color=auto的选项，找到的关键字部分会用特殊颜色显示喔！

#### 排序命令 sort, wc, uniq

很多时候，我们都会去计算一次资料里头的相同型态的资料总数，举例来说， 使用last 可以查得系统上面有登入主机者的身份。那么我可以针对每个使用者查出他们的总登入次数吗？此时就得要排序与计算之类的指令来辅助了！底下我们介绍几个好用的排序与统计指令

##### sort

sort 是很有趣的指令，他可以帮我们进行排序，而且可以依据不同的资料型态来排序喔！例如数字与文字的排序就不一样。此外，排序的字元与语系的编码有关，因此， 如果您需要排序时，建议使用LANG=C 来让语系统一，资料排序比较好一些。

    [dmtsai@study ~]$ sort [-fbMnrtuk] [file or stdin] 
    选项与参数：
    -f ：忽略大小写的差异，例如A与a视为编码相同；
    -b ：忽略最前面的空白字元部分；
    -M ：以月份的名字来排序，例如JAN, DEC等等的排序方法；
    -n ：使用『纯数字』进行排序(预设是以文字型态来排序的)；
    -r ：反向排序；
    -u ：就是uniq ，相同的资料中，仅出现一行代表；
    -t ：分隔符号，预设是用[tab]键来分隔；
    -k ：以那个区间(field)来进行排序的意思

    范例一：个人帐号都记录在/etc/passwd下，请将帐号进行排序。
    [dmtsai@study ~]$ cat /etc/passwd | sort 
    ab rt:x:173:173::/etc/abrt:/sbin/nologin
     ad m:x:3:4:adm:/var/adm:/ sbin/nologin
     al ex:x:1001:1002::/home/alex:/bin/bash
     #鸟哥省略很多的输出～由上面的资料看起来， sort是预设『以第一个』资料来排序，
    #而且预设是以『文字』型态来排序的喔！所以由a开始排到最后啰！

    范例二：/etc/passwd内容是以:来分隔的，我想以第三栏来排序，该如何？
    [dmtsai@study ~]$ cat /etc/passwd | sort -t ':' -k 3 
    root:x: 0 :0:root:/root:/bin/bash 
    dmtsai:x: 1000 :1000:dmtsai:/ home/dmtsai:/bin/bash 
    alex:x: 1001 :1002::/home/alex:/bin/bash 
    arod:x: 1002 :1003::/home/arod:/bin/bash
     #看到特殊字体的输出部分了吧？怎么会这样排列啊？呵呵！没错啦～
    #如果是以文字型态来排序的话，原本就会是这样，想要使用数字排序：
    # cat /etc/passwd | sort -t ':' -k 3 -n 
    #这样才行啊！用那个-n来告知sort以数字来排序啊！

    范例三：利用last ，将输出的资料仅取帐号，并加以排序 
    [dmtsai@study ~]$ last | cut -d ' ' -f1 | sort

##### uniq

如果我排序完成了，想要将重复的资料仅列出一个显示，可以怎么做呢？

    [dmtsai@study ~]$ uniq [-ic] 
    选项与参数：
    -i ：忽略大小写字元的不同；
    -c ：进行计数

    范例一：使用last将帐号列出，仅取出帐号栏，进行排序后仅取出一位； 
    [dmtsai@study ~]$ last | cut -d ' ' -f1 | sort | uniq 

    范例二：承上题，如果我还想要知道每个人的登入总次数呢？
    [dmtsai@study ~]$ last | cut -d ' ' -f1 | sort | uniq -c 
          1 
          6 (unknown 
         47 dmtsai 
          4 reboot 
          7 root 
          1 wtmp #从上面的结果可以发现reboot有4次， root登入则有7次！大部分是以dmtsai来操作！
    # wtmp与第一行的空白都是last的预设字元，那两个可以忽略的！


这个指令用来将『重复的行删除掉只显示一个』，举个例子来说， 你要知道这个月份登入你主机的使用者有谁，而不在乎他的登入次数，那么就使用上面的范例， (1)先将所有的资料列出；(2)再将人名独立出来；(3)经过排序；(4)只显示一个！由于这个指令是在将重复的东西减少，所以当然需要『配合排序过的档案』来处理啰！

##### wc

    [dmtsai@study ~]$ wc [-lwm] 
    选项与参数：
    -l ：仅列出行；
    -w ：仅列出多少字(英文单字)；
    -m ：多少字元；

    范例一：那个/etc /man_db.conf里面到底有多少相关字、行、字元数？

    [dmtsai@study ~]$ cat /etc/man_db.conf | wc 
        131 723 5171 #输出的三个数字中，分别代表： 『行、字数、字元数』范例二：我知道使用last可以输出登入者，但是last最后两行并非帐号内容，那么请问，
            我该如何以一行指令串取得登入系统的总人次？
    [dmtsai@study ~]$ last | grep [a-zA-Z] | grep -v 'wtmp' | grep -v 'reboot' | \ 
    > grep -v 'unknown' |wc -l #由于last会输出空白行, wtmp, unknown, reboot等无关帐号登入的资讯，因此，我利用
    # grep取出非空白行，以及去除上述关键字那几行，再计算行数，就能够了解啰！

举个例子来说， 当你要知道目前你的帐号档案中有多少个帐号时，就使用这个方法：『 cat /etc/passwd | wc -l 』啦！因为/etc/passwd 里头一行代表一个使用者呀！所以知道行数就晓得有多少的帐号在里头了！而如果要计算一个档案里头有多少个字元时，就使用wc -m 这个选项吧！

[详细命令请查看鸟哥博客linux_basic](http://linux.vbird.org/linux_basic/0320bash.php#pipe)





