title: C语言const
date: 2015-09-02 18:23:37
categories:
- C
- const
tags: const
---

## C语言const用法     

const是一个C语言的关键字,它限定一个变量不允许被改变,产生静态作用.    
常类型是指使用类型修饰符const说明的类型,常类型的变量的值是不能改变的.(可以采取别的方式修改值)    

下面的代码错在哪里？     

``` code
    const int n = 5;
    int a[n];
```

为什么编译器会报错,"常量"和"只读变量"是有区别的.常量,例如5,"abc"等等,肯定是只读的,常量被编译器放在只读区域,当然也就不能修改.而"只读变量"则是在内存中开辟一个地方来存放它的值,只不过这个值被编译器限定不允许修改.    

下面的代码哪一句有错误?     

``` code
    typedef char *pStr;
    char string[4] = "bbc";
    const char *p1 = "str";
    const char p2 = "str";
    p1++;
    p2++;
```

答案是`p2++`错误. const的基本使用形式: const type m; 限定m不可变,只读. 可以对应`*p1`不可变,`p1`可变.`p2`不可变    

<!-- more -->

## 指针举例    

下面的const分别修饰什么     

- const在前面

``` code
    const int a;            //a不可变
    const char *p;          //*p不可变,p可变
    const char * const p;   //*p不可变,p可变
```

- const在类型后面

``` code
    int const a;            //a不可变
    char const *p;          //*p不可变,p可变
    char * const p;         //p不可变,*p可变
    char const * const * p; //*p不可变,p不可变
```

分析:const值修饰其后的变量,放在类型前和类型后,并没有区别,`*`不是类型,`*`只是代表改变量是前面的修饰数据类型的指针    

- 多变量一起声明

``` code
    int const *p1, p2;
```

p2是const,`*`和p1是一个整体,`*p1`不可变,p1可变.仅声明了p1为指向int类型的指针.如果两者皆为指向int类型的指针,`int *p1, *p2`即可.    

``` code
    int const * const p1, p2;
```

p2是const,`*p1`是const,被前一个修饰、p1被后一个修饰,也是const    

``` code
    int * const p1, p2;
```

p1是const,`(* const p1)`才是一个整体,所以p2是这样的`int p2`











