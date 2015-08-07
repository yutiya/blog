title: Shell脚本学习第一天
date: 2015-08-07 12:49:50
categories:
- Shell
- course
tags: Shell
---

## Shell脚本学习第一天

### 第一个Shell脚本

``` code
    echo "打印是内容"        //输出内容到stdou 使用双引号""
    read PERSON             //从stdin获取输入并赋值给PERSON变量
    echo "Hello, $PERSON"   //输出内容,同时输出PERSON变量的值
```

### 变量

- 定义变量`varName="value"`,变量名和等号不能有空格    
- 首字符(a-z,A-Z)
- 中间不能有空格,可以使用下划线
- 不能使用标点符号
- 不能使用bash里的关键字

For example:
- `myUrl="http://yutiya.com/"`
- `myNum=100`

使用变量,在变量名前面加$符号即可    
变量名外面的花括号是可选的,是个好习惯    

``` code
    your_name="yutiya"
    echo $your_name
    echo ${your_name}
```

如果不写,像下面这样,就会有问题...

``` code
    for skill in Ada Coffe Action Java 
    do
        echo "I am good at ${skill}Script"
    done
```

这里稍微提一下,Shell中使用#打头备注后面的内容,首行特殊表示用什么脚本解析器执行

<!-- more -->

- 重新定义变量

``` code
    myUrl="http://yutiya.com/"
    echo ${myUrl}

    myUrl="http://yutiya.com/"
    echo ${myUrl}
```

- 只读变量    
- 使用readonly命令定义为只读

``` code
    #!/bin/bash

    myUrl="http://yutiya.com/"
    readonly myUrl
    myUrl="http://yutiya.com/"
```

赋值报错:    
![](/images/shell/course/1.png)

- 删除变量`unset myUrl`

``` code
    #!/bin/bash

    myUrl="http://yutiya.com/"
    unset myUrl
    echo $myUrl
```

没有输出任何内容

### 特殊变量

` $ ehco $$`,打印当前Shell进程的ID,即pid

- `$0`,当前脚本的文件名
- `$n`,传递给脚本或函数的参数,n为数字,表示第几个参数
- `$#`,传递给脚本或函数的参数个数
- `$*`,传递给脚本或函数的所有参数
- `$@`,传递给脚本或函数的所有参数,被双引号""包含时,与$*不同
- `$?`,上个命令的退出状态,或函数的返回值
- `$$`,当前Shell进程ID.对于Shell脚本,就是这些脚本所在的进程ID

For example:

``` code
    #!/bin/bash

    echo "File Name: $0"
    echo "First Parameter: $1"
    echo "First Parameter: $2"
    echo "Quoted Values: $@"
    echo "Quoted Values: $*"
    echo "Total Number of Parameter: $#"
```

![](/images/shell/course/2.png)

- $*和$@的区别:    

``` code
    #!/bin/bash

    echo "\$*=" $*
    echo "\"\$*\"=" "$*"

    echo "\$@=" $@
    echo "\"\$@\"=" "$@"

    echo "print each param from \$*"
    for var in $*
    do
        echo "$var"
    done

    echo "print each param from \$@"
    for var in $@
    do
        echo "$var"
    done

    echo "print each param from \"\$*\""
    for var in "$*"
    do
        echo "$var"
    done

    echo "print each param from \"\$@\""
    for var in "$@"
    do
        echo "$var"
    done
```

![](/images/shell/course/3.png)
可以看出来,使用变量的时候如果采用双引号""形式,$@会自动换行

` $ echo "上一次命令的退出状态" $?`,一般情况下成功为0,失败为1.也可以表示函数返回值

### 变量替换,命令替换,转义字符

- 转义字符:    

``` code
    a=10
    echo -e "Value of a is $a \n"
```

加上-e会把\n解析为换行,否则就直接打印\n

- `\\` 反斜杠
- `\a` 警报
- `\b` 退格
- `\f` 换页
- `\n` 换行
- `\r` 回车
- `\t` 水平制表符(tab键)
- `\v` 垂直制表符

`-E`选项禁止转义,默认不转义,`-n`禁止插入换行符    

- 命令替换(Shell先执行命令,将输出结果暂时保存,在适当的地方输出)    
语法:    
`` `command` ``,这里是反引号,tab键上面那个

``` code
    #!/bin/bash

    DATE=`date`
    echo "Date is $DATE"

    USERS=`who | wc -l`
    echo "Logged in user are $USERS"

    UP=`date ; uptime`
    echo "Uptime is $UP"
```

![](/images/shell/course/4.png)

- 变量替换:    

形式 | 说明
--- | ---
`${var}` | 变量本来的值
`${var:-word}` | 如果变量var为空或已被删除,那么返回word,但不改变var的值
`${var:=word}` | 如果变量var为空或已被删除,那么返回word,并将改变var的值
`${var:?message}` | 如果变量var为空或已被删除,那么将消息messgae送到标准错误输出,可以用来检测变量var是否可以被正常赋值,若此替换出现在Shell脚本中,那么脚本将停止运行
`${var:+word}` | 如果变量var被定义,那么返回word,但不改变var的值

![](images/sheel/course/5.png)

### Shell运算

expr:表达式计算工具

``` code
    #!/bin/bash

    val=`expr 2 + 2`
    echo "Total value: $val"
```

输出结果:`Total value: 4`

注意:
- 变量和运算符之间要有空格,`2 + 2`
- 使用反引号进行命令替换,tab上面的按键

For example:
``` code
    #!/bin/bash

    a=10
    b=20
    val=`expr $a + $b`
    echo "a + b : $val"

    val=`expr $a - $b`
    echo "a - b : $val"

    val=`expr $a \* $b`
    echo "a * b : $val"

    val=`expr $b / $a`
    echo "b / a : $val"

    val=`expr $b % $a`
    echo "b % a : $val"

    if [ $a == $b ]
    then
       echo "a is equal to b"
    fi

    if [ $a != $b ]
    then
       echo "a is not equal to b"
    fi
```

![](/images/shell/course/6.png)

运算符 | 说明
--- | ---
`+` | 加法
`-` | 减法
`*` | 乘法
`/` | 除法
`%` | 取余
`=` | 赋值
`==` | 相等.用于比较两个数字，相同则返回true
`!=` | 不相等.用于比较两个数字,不相同则返回true

- 关系运算(只支持数字,不支持字符串,除非字符串的值是数字):    

``` code
    #!/bin/bash

    a=10
    b=20
    if [ $a -eq $b ]
    then
       echo "$a -eq $b : a is equal to b"
    else
       echo "$a -eq $b: a is not equal to b"
    fi

    if [ $a -ne $b ]
    then
       echo "$a -ne $b: a is not equal to b"
    else
       echo "$a -ne $b : a is equal to b"
    fi

    if [ $a -gt $b ]
    then
       echo "$a -gt $b: a is greater than b"
    else
       echo "$a -gt $b: a is not greater than b"
    fi

    if [ $a -lt $b ]
    then
       echo "$a -lt $b: a is less than b"
    else
       echo "$a -lt $b: a is not less than b"
    fi

    if [ $a -ge $b ]
    then
       echo "$a -ge $b: a is greater or  equal to b"
    else
       echo "$a -ge $b: a is not greater or equal to b"
    fi

    if [ $a -le $b ]
    then
       echo "$a -le $b: a is less or  equal to b"
    else
       echo "$a -le $b: a is not less or equal to b"
    fi
```

![](/images/shell/course/7.png)

运算符 | 说明
--- | ---
`-eq` | 检测两个数是否相等,相等返回true
`-ne` | 检测两个数是否相等,不相等返回true
`-gt` | 检测左边的数是否大于右边的,如果是,则返回true
`-lt` | 检测左边的数是否小于右边的,如果是,则返回true
`-ge` | 检测左边的数是否大于等于右边的,如果是,则返回true
`-le` | 检测左边的数是否小于等于右边的,如果是,则返回true

- 布尔运算符:    

``` code
    #!/bin/bash

    a=10
    b=20

    if [ $a != $b ]
    then
       echo "$a != $b : a is not equal to b"
    else
       echo "$a != $b: a is equal to b"
    fi

    if [ $a -lt 100 -a $b -gt 15 ]
    then
       echo "$a -lt 100 -a $b -gt 15 : returns true"
    else
       echo "$a -lt 100 -a $b -gt 15 : returns false"
    fi

    if [ $a -lt 100 -o $b -gt 100 ]
    then
       echo "$a -lt 100 -o $b -gt 100 : returns true"
    else
       echo "$a -lt 100 -o $b -gt 100 : returns false"
    fi

    if [ $a -lt 5 -o $b -gt 100 ]
    then
       echo "$a -lt 100 -o $b -gt 100 : returns true"
    else
       echo "$a -lt 100 -o $b -gt 100 : returns false"
    fi
```

运算符 | 说明
--- | ---
`!` | 非运算,表达式为true则返回false,否则返回true
`-o` | 或运算,有一个表达式为true则返回true
`-a` | 与运算,两个表达式都为true才返回true

- 字符串运算符:    

``` code
    #!/bin/bash

    a="abc"
    b="efg"

    if [ $a = $b ]
    then
       echo "$a = $b : a is equal to b"
    else
       echo "$a = $b: a is not equal to b"
    fi

    if [ $a != $b ]
    then
       echo "$a != $b : a is not equal to b"
    else
       echo "$a != $b: a is equal to b"
    fi

    if [ -z $a ]
    then
       echo "-z $a : string length is zero"
    else
       echo "-z $a : string length is not zero"
    fi

    if [ -n $a ]
    then
       echo "-n $a : string length is not zero"
    else
       echo "-n $a : string length is zero"
    fi

    if [ $a ]
    then
       echo "$a : string is not empty"
    else
       echo "$a : string is empty"
    fi
```

![](/images/shell/course/8.png)

运算符 | 说明
--- | ---
`=` | 检测两个字符串是否相等,相等返回true
`!=` | 检测两个字符串是否相等,不相等返回true
`-z` | 检测字符串长度是否为0,为0返回true
`-n` | 检测字符串长度是否为0,不为0返回true
`str` | 检测字符串是否为空,不为空返回true(直接判断变量)

- 文件测试运算符:    
检测unix文件的各种属性

``` code
    #!/bin/bash

    file="/Users/admin/Desktop/blogrefer/test.sh"

    if [ -r $file ]
    then
       echo "File has read access"
    else
       echo "File does not have read access"
    fi

    if [ -w $file ]
    then
       echo "File has write permission"
    else
       echo "File does not have write permission"
    fi

    if [ -x $file ]
    then
       echo "File has execute permission"
    else
       echo "File does not have execute permission"
    fi

    if [ -f $file ]
    then
       echo "File is an ordinary file"
    else
       echo "This is sepcial file"
    fi

    if [ -d $file ]
    then
       echo "File is a directory"
    else
       echo "This is not a directory"
    fi

    if [ -s $file ]
    then
        echo "File size is not zero"
    else
        echo "File size is zero"
    fi

    if [ -e $file ]
    then
       echo "File exists"
    else
       echo "File does not exist"
    fi
```

![](/images/shell/course/9.png)

操作符 | 说明
--- | ---
`-b file` | 检测文件是否是块设备文件,如果是,则返回true
`-c file` | 检测文件是否是字符设备文件,如果是,则返回true
`-d file` | 检测文件是否是目录,如果是,则返回true
`-f file` | 检测文件是否是普通文件(既不是目录,也不是设备文件),如果是,则返回true
`-g file` | 检测文件是否设置了SGID位,如果是,则返回true
`-k file` | 检测文件是否设置了粘着位(Sticky Bit),如果是,则返回true
`-p file` | 检测文件是否是具名管道,如果是,则返回true
`-u file` | 检测文件是否设置了SUID位,如果是,则返回true
`-r file` | 检测文件是否可读,如果是,则返回true
`-w file` | 检测文件是否可写,如果是,则返回true
`-x file` | 检测文件是否可执行,如果是,则返回true
`-s file` | 检测文件是否为空(文件大小是否为0),不为空返回true
`-e file` | 检测文件(包括目录)是否存在,如果是,则返回true

### Shell字符串

- 单引号: `str='this is a string'`

注意:    
- 单引号里的任何字符都会原样输出,使用的变量无效
- 单引号字符串中不能出现单引号(对单引号使用转义符后也不行)
- 双引号: `str="Hello, I know you are \"$your_name\"! \n"`,可以使用变量,可以出现转义字符
- 字符串拼接

``` code
    your_name="yutiya"
    greeting="hello, "$your_name" !"
    greeting_1="hello, ${your_name} !"

    echo $greeting $greeting_1
```

- 获取字符串长度

``` code
    string="abcd"
    echo ${#string} #输出 4
```

- 取子字符串

``` code
    string="alibaba is a great company"
    echo ${string:1:4} #输出liba
```
- 查找子字符串

注意: mac终端的expr命令只能进行加减乘除

``` code
    string="alibaba is a great company"
    echo `expr index "$string" is` # 输出3,只匹配is中的i
```

### 数组

``` code
    array_name=(value0 value1 value2 value3)
    或者
    array_name=(
        value0
        value1
        value2
        value3
    )
    也可以这样
    array_name[0]=value0
    array_name[1]=value1
    array_name[2]=value2
```

可以不使用连续的下标,而且下标的范围没有限制

- 读取:```value=${array_name[2]}```
- 获取所有元素:```${array_name[*]}```或者```${array_name[@]```
- 获取数组的长度:```length=${#array_name[@]}```或者```length=${array_name[*]}```
- 取得数组单个元素的长度:```length=${#array_name[n]}```

### echo

- 普通` $ echo arg`
- 转义` $ echo "\" It is a test \""`
- 变量` $ echo "$name is a number"`,变量与其他字符相连请加上`${name}`
- 换行` $ echo "Oh! \n"`
- 不换行` $ echo "OK! \c"`
- 结果输出到文件` $ echo "test" > filename`
- 原样输出` $ echo 'chars'`
- 显示命令结果`` `date` ``

**双引号可有可无,单引号原样输出**
