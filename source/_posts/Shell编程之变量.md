---
title: Shell编程之变量
date: 2016-01-04 22:34:27
tags: 
    - shell
    - linux
---

### 变量

变量的配置守则

1. `变量与变量内容以一个等号『=』来连结`，如下所示：
    『myname=VBird』
2. `等号两边不能直接接空格符`，如下所示为错误： 
    『myname = VBird』或『myname=VBird Tsai』
3. 变量名称只能是英文字母与数字，但是`开头字符不能是数字`，如下为错误： 
    『2myname=VBird』
4. 变量内容若有空格符可使用双引号『\"』或单引号『\'』将变量内容结合起来，但
    + `双引号内的特殊字符如 $ 等，可以保有原本的特性`，如下所示：
        『var="lang is $LANG"』则『echo $var』可得『lang is en_US』
    + `单引号内的特殊字符则仅为一般字符 (纯文本)`，如下所示：
        『var='lang is $LANG'』则『echo $var』可得『lang is $LANG』
5. 可用`转义字符『 \ 』`将特殊符号(如 [Enter], $, \, 空格符, `等)变成一般字符；
6. 在一串命令中，还需要藉由其他的命令提供的信息，可以`使用反单引号『`命令`』或 『$(命令)』`。特别注意，那个 ` 是键盘上方的数字键 1 左边那个按键，而不是单引号！ 例如想要取得核心版本的配置：
    『version=$(uname -r)』再『echo $version』可得『2.6.18-128.el5』
7. 若该变量为扩增变量内容时，则可用 "$变量名称" 或 ${变量} 累加内容，如下所示：
    『PATH="$PATH":/home/bin』
8. 若该变量需要在其他子程序运行，则`需要以 export 来使变量变成环境变量`：
    『export PATH』
9. 通常大写字符为系统默认变量，自行配置变量可以使用小写字符，方便判断 (纯粹依照使用者兴趣与嗜好) ；
10. `取消变量的方法为使用 unset` ：『unset 变量名称』例如取消 myname 的配置：
    『unset myname』
11. `在Bash中，变量的默认类型是字符串型`

<!-- more -->

### 变量分类

1. 用户自定义变量
2. 环境变量：保存和系统操作环境相关的数据。变量可以自定义，但是对系统生效的环境变量名和变量作用是固定的。
3. 位置参数变量：主要用来向脚本当中传递参数或数据，变量名不能自定义，变量的作用是固定的
4. 预定义变量：Bash中已经定义好的变量，变量名不能自定义，变量的作用是固定的



### 用户自定义变量

#### 变量赋值：**变量名=变量值**

    ➜  ~ x=5                <== 正确
    ➜  ~ y= "happy"         <== 「=」两边不能存在空格，否则报错
    zsh: command not found: happy
    ➜  ~  name="larry s"    <== 变量内容中有空格用双引号括起来

#### 变量调用： **$变量名**

    ➜  ~  echo $x
    5                       <== 5为字符串
    ➜  ~  echo $y
    6
    ➜  ~  z=$x+$y
    ➜  ~  echo $z
    5+6                     <== 不能直接进行整型计算
    ➜  ~  echo $name
    larry s

#### 变量叠加：**"$变量名"叠加内容/${变量名}叠加内容**

    ➜  ~  x=123             <== 覆盖原值5
    ➜  ~  echo $x
    123
    ➜  ~  x="$x"456         <== 变量叠加方式一
    ➜  ~  echo $x
    123456
    ➜  ~  x=${x}789         <== 变量叠加方式二，推荐
    ➜  ~  echo $x
    123456789

#### 变量查看：**set** 

查询系统中所有变量

    ➜  ~  set
    '!'=0
    '#'=0
    '$'=11921
    '*'=()
    -=3569JNRTXZghiklms
    0=-zsh
    '?'=0
    @=()
    ARGC=0
    .
    .
    .
    x=123456789
    y=6
    z=5+6
    zsh_eval_context=(toplevel)
    zsh_scheduled_events

**set -u**

    ➜  ~  echo $larry
                                    <== larry变量可能不存在或者值为空,输出空
    ➜  ~  set -u
    ➜  ~  echo $larry
    zsh: larry: parameter not set   <== 设置『set -u』后会明确报错

#### 删除变量：unset 变量名

    ➜  ~  unset x
    ➜  ~  unset y
    ➜  ~  echo $x
    zsh: x: parameter not set
    ➜  ~  echo $y
    zsh: y: parameter not set

### 环境变量

#### 变量赋值 

    export 变量名=变量值

或者 

    变量名=变量值

    export 变量名

#### 变量查看

    set 查看所有变量
    env 查看环境变量 

#### 删除变量

    unset 变量名

### 位置变量参数

**$n**: n为数字，$0代表命令本身，$1-$9代表第一到第九个参数，10以上的参数用大括号，如${10}
    
    ```bash
    #!/bin/bash
    #位置变量参数$n演示
    num1=$1
    num2=$2
    sum=$(( $num1 + $num2 ))    #『 $((计算式)) 』来进行数值运算两个小括号内可以加上空白字节
    echo $sum
    ```
    //运行输出
    ➜  shellnotes  ./sh1.sh 6 9 <==$1为6，$2为9
    15                          <==6+9=15

**$\***: 代表命令行中所有的参数，$*把所有的命令看成一个整体

**$@**: 也代表命令行中所有的参数，不过$@把每个参数区分对待

**$#**: 代表命令行中所有参数的个数

    ```bash
    #!/bin/bash
    #$*,$@,$#演示
    echo "The parameter is : $*"
    echo "The parameters are : $@"
    echo "Parameters size is : $#"
    ```
    //运行输出
    ➜  shellnotes  ./sh2.sh 11 22 33 44 55  <== 输入4个参数
    The parameter is : 11 22 33 44 55   <==$*把输入的参数看做一个整体
    The parameters are : 11 22 33 44 55 <==$@把输入的参数每个都区别对待
    Parameters size is : 5

用循环看下$\*和$@的区别
    
    ```bash
    #!/bin/bash
    #for区分$*和$@
    for i in "$*"           
        do
                echo "\$*:$i"
        done
    for j in "$@"           
        do
                echo "\$@:$j"
        done
    ```
    //运行输出
    ➜  shellnotes  ./for.sh 11 22 33 44
    $*:11 22 33 44                      <== 循环1次
    $@:11                               <== 参数有几个循环几次
    $@:22
    $@:33
    $@:44

### 预定义变量

**$?**:最后一次执行命令的返回状态。如果这个变量的值为0，证明上一个命令执行正确；如果这个值非0，则证明上一个命令执行不正确。

    [root@www ~]# echo $SHELL
    /bin/bash                                  <==可顺利显示！没有错误！
    [root@www ~]# echo $?
    0                                          <==因为没问题，所以回传值为 0
    [root@www ~]# 12name=VBird
    -bash: 12name=VBird: command not found     <==发生错误了！bash回报有问题
    [root@www ~]# echo $?
    127                                        <==因为有问题，回传错误代码(非为0)
    # 错误代码回传值依据软件而有不同，我们可以利用这个代码来搜寻错误的原因喔！
    [root@www ~]# echo $?
    0
    # 咦！怎么又变成正确了？这是因为 "?" 只与『上一个运行命令』有关，
    # 所以，我们上一个命令是运行『 echo $? 』，当然没有错误，所以是 0 没错！

**$$**:当前进程的进程ID(PID)

    ➜  ~  echo $$
    14759

**$!**:后台运行的最后一个进程的进程号(PID)

    ➜  ~  echo $!
    0

变量详细请看鸟哥的博客[认识学习变量](http://linux.vbird.org/linux_basic/0320bash.php#variable)


