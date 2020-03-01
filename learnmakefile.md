### linux编译过程

微观的C/C++编译执行过程

几个过程：

-E 预处理：把 .c .h 展开成一个文件，宏定义直接替换 头文件 库文件 .i

gcc -E hello.c -o hello.i

-S 汇编：.i生成一个汇编代码文件 .S

gcc -S hello.i -o hello.s

-c 编译：.S生成一个.o .obj

gcc -c hello.s -o hello.o

-o 链接：.o链接  .exe windows .elf

gcc hello.o -o hello

四行合并成一行 gcc hello.c -o hello



### 脚本语言 Makefile

#### 第一层

显示规则

目标文件：依赖文件

[Tab]指令



第一个目标文件是我的最终目标

rm -rf

伪目标.PHONY



test:$(OBJ)

​	$(CC) $^ -o $@

.PHONY:clean rebuild

clean:

​		rm -f *.o test

rebuild:clean test

#### 第二层

变量

=(替换)

+=（追加）

:=（恒等于）

使用变量的时候$(变量名)

用户自定义变量，自动变量，预定义变量，环境变量

##### 自定义变量

定义变量格式如：变量名:=变量值

如何引用变量：$(变量名)=???//赋值 ，??? = $(变量名)//引用

```makefile
SOURCE:=a.o b.o c.o
EXE:=test
$(EXE):$(SOURCE)
	gcc $(SOURCE) -o $(EXE)
```

##### 自动变量

使用的时候，自动用特定的值替换。Makefile自带的变量

| ---   | ---                                                   |
| ----- | ----------------------------------------------------- |
| $@    | 当前规则的目标文件                                    |
| $<    | 当前规则的第一个依赖文件                              |
| $^    | 当前规则的所有依赖文件                                |
| $?    | 规则中日期新于目标文件的所有相关文件列表，逗号隔开    |
| $(@D) | 目标文件的目录名部分，若$@为add/add.c，则$(@D)为add   |
| $(@F) | 目标文件的文件名目标，若$@为add/add.c，则$(@F)为add.c |

```makefile
SOURCE:=a.o b.o c.o
EXE:=test
$(EXE):$(SOURCE)
	gcc $^ -o $@
a.o:a.c
	gcc -c $^ -o $@
b.o:b.c
	gcc -c $^ -o $@
c.o:c.c
	gcc -c $^ -o $@
```

##### 预定义变量和环境变量

都是系统里设定好的变量，值是固定的，有些值为空

linux里查看环境变量：export

|          |                           |
| -------- | ------------------------- |
| AS       | 汇编程序，默认为as        |
| CC       | c编译器，默认为cc         |
| CPP      | c预编译器，默认为$(CC) -E |
| CXX      | c++编译器，默认为g++      |
| RM       | 删除，默认为 rm -f        |
| ARFLAGS  | 库选项，无默认            |
| ASFLAGS  | 汇编选项，无默认          |
| CFLAGS   | c编译器选择，无默认       |
| CPPFLAGS | c预处理器选项，无默认     |
| CXXFLAGS | c++编译器选择，无默认     |

#### 第三层

分为：普通规则，隐含规则，模式规则

##### 隐含规则

1、*.o文件自动依赖 *.c文件，可以省略main.o:main.c

```makefile
SOURCE:=a.o b.o c.o
EXE:=test
$(EXE):$(SOURCE)
	gcc $^ -o $@
```

2、模式规则

通过匹配模式找字符串，%匹配一个或多个任意字符串

%.o： %.c 任何目标文件的依赖文件是与目标文件同名的且扩展名为.c的文件

```makefile
SOURCE:=a.o b.o c.o
EXE:=test
$(EXE):$(SOURCE)
	gcc $^ -o $@
%.o:%.c
	gcc -c $^ -o $@
```

另外还可以指定将*.o， *.exe， *.a， *.so等编译到指定的目录中

```makefile
DIR:=./debug/
$(DIR)out:$(DIR)test.o $(DIR)sequence.o
	gcc $(DIR)test.o $(DIR)sequence.o -o $(DIR)out
$(DIR)sequence.o:sequence.c
	gcc -c sequence.c -o $(DIR)sequence.o
$(DIR)test.o:test.c
	gcc -c  test.c -o $(DIR)test.o
```

#### 第四层

通配符

$@ 所有目标文件

$^ 所有依赖文件

$< 所有的依赖文件的第一个文件 

#### 第五层

函数

1、搜索文件函数wildcard

搜索当前目录下的文件名，展开成一列所有符合其参数描述的文件名，文件间以空格间隔

SOURCE = $(wildcard *cpp)//把当前目录下的所有.cpp文件存入变量SOURCE中

2、字符串替换函数patsbust

格式：$(patsubst 要查找的子串，替换后的目标子串，源字符串)

将源字符串中所有要查找的子串替换成目标子串。

OBJS = $(patsubst %.cpp,%.o,$(SOURCE))

把SOURCE中的.cpp替换为.o，然后把替换后字符串放入OBJS中

3、加前缀函数addprefix

格式：$(addprefix 前缀，源字符串)

该函数把第二个参数列表的每一项前缀上第一个参数值



#### 其他

1、makefile里注释使用#

2、命令如果不想显示到终端，在命令前加@

3、如果命名的makefile文档名，并非makefile，那么需要加上 -f filename

4、makefile文件如果拆成若干个.mk文件，在主makefile中，使用include关键字把其他.mk文件包含进来

| -       | -                                              |
| ------- | ---------------------------------------------- |
| -C dir  | 读入指定目录下的makefile                       |
| -f file | 读入当前目录下的file文件作为makefile           |
| -i      | 忽略所有的命令执行错误                         |
| -I dir  | 指定被包含的makefile所在目录                   |
| -n      | 只打印要执行的命令，但不执行这些命令           |
| -p      | 显示make变量数据库和隐含规则                   |
| -s      | 在执行命令时不显示命令                         |
| -w      | 如果make在执行过程中改变目录，则打印当前目录名 |



