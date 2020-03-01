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

使用的时候，自动用特定的值替换

$@：当前规则的目标文件

$<：当前规则的第一个依赖文件

$^：当前规则的所有依赖文件

$?：规则中日期新于目标文件的所有相关文件列表，逗号隔开

$(@D)：目标文件的目录名部分，若$@为add/add.c，则$(@D)为add

$(@F)：目标文件的文件名目标，若$@为add/add.c，则$(@F)为add.c

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

#### 第三层

隐含规则

%.c %.o 任意的.c .o文件

*.c *.o 所有的.c .o文件

#### 第四层

通配符

$@ 所有目标文件

$^ 所有依赖文件

$< 所有的依赖文件的第一个文件 

#### 第五层

函数

