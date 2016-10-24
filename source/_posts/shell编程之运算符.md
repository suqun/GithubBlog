---
title: shell编程之运算符
date: 2016-01-09 12:14:05
tags: 
    - Shell
    - Linux
toc: true
---

### 声明变量类型declare

declare语法：

```bash
    declare [-afiprx] 变量名
    参数说明： 
    -   ：设置属性
    +   ：取消属性
    -a  ：定义为数组 array 
    -f  ：定义为函数 function  
    -i  ：定义为整数 integer 
    -p  ：显示变量被声明的类型
    -r  ：定义为『只读』 
    -x  ：定义为环境变量 

    bash-3.2$ declare -i a=47
    bash-3.2$ declare -i b=53
    bash-3.2$ declare -i c=$a+$b
    bash-3.2$ echo $c
    100
    bash-3.2$ declare -p c
    declare -i c="100"

    bash-3.2$ declare -a arr[0]=11  <==定义数组
    bash-3.2$ declare -p arr
    declare -a arr='([0]="11")'
    bash-3.2$ declare -a arr[1]=22
    bash-3.2$ declare -p arr
    declare -a arr='([0]="11" [1]="22")'
    bash-3.2$

    bash-3.2$ echo ${arr}       <==查看数组方法
    11
    bash-3.2$ echo ${arr[1]}
    22
    bash-3.2$ echo ${arr[*]}
    11 22
    bash-3.2$

```

<!-- more -->

### 数值运算

数值运算可以通过`declare -i c=$a+$b`进行，也可以用`expr`/`$((运算式))`/`$[运算式]`

```bash
    bash-3.2$ dd=$(($a+$b))
    bash-3.2$ echo $dd
    100
    bash-3.2$ ee=$(expr $a + $b)    <==『+』两边必须有空格
    bash-3.2$ echo $ee
    100
    bash-3.2$ ff=$(($a+$b))
    bash-3.2$ echo $ff
    100
    bash-3.2$ gg=$[$a+$b]
    bash-3.2$ echo $gg
    100
```

shell支持的运算符有

![shell运算符](http://7xpk5e.com1.z0.glb.clouddn.com/shell-ysf.png)


### 变量测试

下面的运算符我的理解类似于java的`？：`三目运算符，比如第一行的`x=${y-新值}`，意思是，如果y没有设置则x=新值，如果y为空值则x=空，如果y有值则x=$y。其他的类推

![shell变量测试运算符](http://7xpk5e.com1.z0.glb.clouddn.com/shell-test-ysf.png)

shell编程视频地址：http://www.imooc.com/learn/355


    