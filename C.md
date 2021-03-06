# 创建

C程序创建的基本步骤：
编写：
编译：（转成机器语言，以.o .obj结尾），
链接：
执行：
# 简单结构
```
#include <stdio.h> /*预处理指令，里面的文件为头文件/*

int main(){ /*主函数，只有一个/*
 /* 我的第一个 C 程序 */
 XX；
}

printf()
```

区分大小写

## 数据类型
基本类型：整数，浮点数

枚举类型：

void类型：指定没有可用的值

派生类型：


## 变量 and 常量：
extern int i；//声明外部变量，不是定义

整数常量 如0x212L

浮点常量 如3.14159E-5L

字符常量 可以是一个普通的字符（例如 'x'）、一个转义序列（例如 '\t'），或一个通用的字符（例如 '\u02C0'）

字符串常量 "hello, dear"

定义常量： 使用#define 或 const 


```
#define identifier value
#define LENGTH 10
const int var = 5
```
## 存储类
存储类定义 C 程序中变量/函数的范围（可见性）和生命周期

auto： 局部变量默认的存储类

register：定义存储在寄存器中，用于快速访问的变量

static：指示编译器在程序的生命周期内保持局部变量的存在，而不需要在每次它进入和离开作用域时进行创建和销毁。

因此，使用 static 修饰局部变量可以在函数调用之间保持局部变量的值。

static 修饰符也可以应用于全局变量。当 static 修饰全局变量时，会使变量的作用域限制在声明它的文件内。

extern：用来在另一个文件中声明一个全局变量或函数。

## 运算符

算数，关系，逻辑，位，赋值，杂项

& 返回变量地址

* 根据地址返回变量

sizeof()返回变量大小

## 判断
Exp1 ? Exp2 : Exp3;
如果 Exp1 为真，则计算 Exp2 的值，结果即为整个表达式的值。
如果 Exp1 为假，则计算 Exp3 的值，结果即为整个表达式的值。
```
switch(表达式)
{
    case 常量表达式1:语句1;
    case 常量表达式2:语句2;
    ...
    default:语句n+1;
}
```
可用break跳出case

## 函数

```
return_type function_name( parameter list)
{
 body of the function
}
```
调用方式： 

传值调用 复制给形参 修改函数内形参不改变实际参数。默认方式

引用调用 复制参数的地址给形参 会改变实参

## 指针
代表内存位置的16进制数

type *var-name

Null 空指针 内存位置为0，假定为不指向任何东西

## 数组
以存储一个固定大小的相同类型元素的顺序集合

type arrayName [ arraySize ];

arrayname[num索引] 访问元素

## 结构体

可存储不同类型数据项的变量

使用.访问成员

可作为函数参数： viod printBook( struct Book book)    /* 声明函数内book变量，类型为BOOK */
```
struct [struct tag]
{
 
}[one or more struct var]
```
指向结构的指针
```
#include <stdio.h>
#include <string.h>
 
struct Books
{
   char  title[50];  可容纳50个char 的数组title
   char  author[50];
   char  subject[100];
   int   book_id;
};
 
/* 函数声明 */
void printBook( struct Books *book );
int main( )
{
   struct Books Book1;        /* 声明 Book1，类型为 Books */

   /* Book1 详述 */
   strcpy( Book1.title, "C Programming");
   strcpy( Book1.author, "Nuha Ali"); 
   strcpy( Book1.subject, "C Programming Tutorial");
   Book1.book_id = 6495407;
 
   /* 通过传 Book1 的地址来输出 Book1 信息 */
   printBook( &Book1 );
 
   /* 通过传 Book2 的地址来输出 Book2 信息 */
   printBook( &Book2 );
 
   return 0;
}
void printBook( struct Books *book )
{
   printf( "Book title : %s\n", book->title);
   printf( "Book author : %s\n", book->author);
   printf( "Book subject : %s\n", book->subject);
   printf( "Book book_id : %d\n", book->book_id);
}
```
## 位域
带有预定义宽度的变量

从0-7的变量。
```
struct {
  unsigned int age:3;
} Age;
```

## 字符串
使用null字符'' 终止的字符数组

char greeting[6] = {'H','e','l','l','o',''};

OR

char greeting[] = "Hello";

## 共用体
在相同的内存位置存储不同的数据类型。任何时候只有一个成员带有值
```
union DATA
{
 int i ;
 float f;
 char str[20];
} data;

将占用20字节

```
## typeof
可为类型取新的名字

```
typedef struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} Book;
 

   Book book;
 
```

### 预处理器
会指示编译器在实际编译之前完成所需的预处理（文本替换工具）

宏： 使用#define 定义用于替换的字符串 ， 增强可读性

#include 包含一个源代码文件

```
#undef 取消已定义的宏
#ifdef 如果宏已经定义，则返回真
#ifndef 如果宏没有定义，则返回真

#ifndef MESSAGE
   #define MESSAGE "You wish!"
#endif

预定义宏：
__DATE__	当前日期，一个以 "MMM DD YYYY" 格式表示的字符常量。
__TIME__	当前时间，一个以 "HH:MM:SS" 格式表示的字符常量。
__FILE__	这会包含当前文件名，一个字符串常量。
__LINE__	这会包含当前行号，一个十进制常量。
__STDC__	当编译器以 ANSI 标准编译时，则定义为 1。

宏处理器运算符
宏延续运算符 \
字符串常量化运算符 #     把一个宏的参数转换为字符串常量
#include <stdio.h>
#define  message_for(a, b)  \
    printf(#a " and " #b ": We love you!\n")
int main(void)
{
   message_for(Carole, Debra);
   return 0;
}

标记粘贴运算符 ##
defined 运算符   用来确定一个标识符是否已经使用 #define 定义过
```
参数化的宏 ： 可以用来模拟函数

#define MAX(x,y) ((x) > (y) ? (x) : (y))

### 头文件
头文件是扩展名为 .h 的文件，包含了 C 函数声明和宏定义，被多个源文件中引用共享。有两种类型的头文件：程序员编写的头文件和编译器自带的头文件。

引用系统头文件：

#include <file>
 
引用用户头文件
 
 #include "file"
```
 只引用一次头文件:
#ifndef HEADER_FILE
#define HEADER_FILE
 这种结构就是通常所说的包装器 #ifndef。当再次引用头文件时，条件为假，因为 HEADER_FILE 已定义。此时，预处理器会跳过文件的整个内容，编译器会忽略它。
 
 有条件引用
 #define SYSTEM_H "system_1.h"

 #include SYSTEM_H
 YSTEM_H 可通过 -D 选项被您的 Makefile 定义。
 
```

 ### 内存管理
 
 
