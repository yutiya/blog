title: Shell脚本学习第二天
date: 2015-08-07 15:54:02
categories:
- Shell
- course
tags: Shell
---

## 第二天

### 格式化输出

必须显示添加换行符

``` code
    printf "Hello, Shell\n"
```

printf命令语法:    
`printf format-string [arguments...]`    

``` code
    #!/bin/bash

    printf "%d %s \n" 1 "abc"
    # 单引号和双引号一样
    printf '%d %s \n' 2 "bcd"
    # 即使不使用引号也一样
    printf %s cde
    # 多余的参数仍然遵循现有格式
    printf %s abc def

    printf "%s\n" abc def
    # 未设置参数 %s 被NULL代替  %d被0代替
    printf "%s and %d \n"
    # 参数传递错误 
    printf "The first program always prints '%s, %d' \n" "Hello" "Shell"
```

![](/images/shell/course2/1.png)

<!-- more -->

### if...else语句

if语句:   

``` code
    if [ expression ]
    then
        # do something
    fi
```

if...else语句:   

``` code
    if [ expression ]
    then
        # do something
    else
        # do something
    fi
```

多层使用:    

``` code
    if [ expression 1 ]
    then
        # do something
    elif [ expression 2]
    then
        # do something
    else
        # do something
    fi
```

if...else语句也可以写成一行,以命令行的方式来运行
``` code
    if test $[2*3] -eq $[1+5]; then echo "The two numbers are equal!"; fi;
```

test命令用于检测条件是否成立,与方括号[]类似    
`$[]`还计算了结果保存到变量中    

### 分支语句

``` code
    case 值 in
        结果1)
            # do something
            ;;
        模式2)
            #do something
            ;;
        *)      # 相当于default
        ;;      
    esac
```

``` code
    #!/bin/bash

    option="${1}"
    case ${option} in
       -f) FILE="${2}"
          echo "File name is $FILE"
          ;;
       -d) DIR="${2}"
          echo "Dir name is $DIR"
          ;;
       *) 
          echo "`basename ${0}`:usage: [-f file] | [-d directory]"
          exit 1 # 命令结束,返回状态1
          ;;
    esac
```

![](/images/shell/course2/2.png)

### for循环

``` code
for 变量 in 列表
do
    # do something
done
```

列表为一组值, 每个值通过空格分隔,每循环一次,就将列表中的下一个值赋给变量.    
列表是可选的,如果不用它,for循环使用命令行的位置参数

显示主目录下以 .bash 开头的文件：

``` code
    #!/bin/bash

    for FILE in $HOME/.bash*
    do
       echo $FILE
    done
```

![](/images/shell/course2/3.png)

### while循环

``` code
    while 条件
    do
        # do something
    done
```

### until循环

``` code
    until 条件
    do
        # do something
    done
```

``` code
    #!/bin/bash

    a=0

    until [ ! $a -lt 10 ]
    do
       echo $a
       a=`expr $a + 1`
    done
```

![](/images/shell/course2/4.png)

break 跳出循环
break n 跳出n层循环

continue 结束本次循环

### 函数

``` code
    function_name(){
    }
    或者
    function function_name(){
    }
```

``` code
    #!/bin/bash

    Hello () {
        echo "Hello World!"
    }
    # Invoke your custom function
    Hello
```

``` code
    #!/bin/bash
    funWithReturn(){
        echo "The function is to get the sum of two numbers..."
        echo -n "Input first number: "
        read aNum
        echo -n "Input another number: "
        read anotherNum
        echo "The two numbers are $aNum and $anotherNum !"
        return $(($aNum+$anotherNum))
    }
    funWithReturn
    # 捕获返回值使用$?
    ret=$?
    echo "The sum of two numbers is $ret !"
```

![](/images/shell/course2/5.png)

使用`unset .f function_name`来删除函数    
如果你希望直接从终端调用函数,可以将函数定义在主目录下的 .profile 文件,这样每次登录后，在命令提示符后面输入函数名字就可以立即调用.

### 函数参数

``` code
    #!/bin/bash

    funWithParam(){
        echo "The value of the first parameter is $1 !"
        echo "The value of the second parameter is $2 !"
        echo "The value of the tenth parameter is $10 !"
        echo "The value of the tenth parameter is ${10} !"
        echo "The value of the eleventh parameter is ${11} !"
        echo "The amount of the parameters is $# !"  # 参数个数
        echo "The string of the parameters is $* !"  # 传递给函数的所有参数
    }
    funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

![](/images/shell/course2/6.png)

特殊变量 | 说明
--- | ---
$# | 传递给函数的参数个数
$* | 显示所有传递给函数的参数
$@ | 与`$*`相同,但是略有区别,请查看Shell学习第一天
$? | 函数的返回值

### 输入输出重定向

一般情况下,每个 Unix/Linux 命令运行时都会打开三个文件:   
- 标准输入文件(stdin):stdin的文件描述符为0,Unix程序默认从stdin读取数据
- 标准输出文件(stdout):stdout 的文件描述符为1,Unix程序默认向stdout输出数据
- 标准错误文件(stderr):stderr的文件描述符为2,Unix程序会向stderr流中写入错误信息

命令 | 说明
--- | ---
command > file | 将输出重定向到 file
command < file | 将输入重定向到 file
command >> file | 将输出以追加的方式重定向到 file
n > file | 将文件描述符为 n 的文件重定向到 file
n >> file | 将文件描述符为 n 的文件以追加的方式重定向到 file
n >& m | 将输出文件 m 和 n 合并
n <& m | 将输入文件 m 和 n 合并
<< tag | 将开始标记 tag 和结束标记 tag 之间的内容作为输入

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null:   
` $ command > /dev/null`

`/dev/null`是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 `/dev/null` 文件非常有用，将命令的输出重定向到它，会起到”禁止输出“的效果
如果希望屏蔽 stdout 和 stderr，可以这样写:
` $ command > /dev/null 2>&1`

Here Document:    
Here Document 目前没有统一的翻译,这里暂译为”嵌入文档“.Here Document 是 Shell 中的一种特殊的重定向方式,它的基本的形式如下:

``` code
    command << delimiter
        document
    delimiter
```

它的作用是将两个 delimiter 之间的内容(document) 作为输入传递给 command

### 文件包含

`. filename`或者`source filename`


