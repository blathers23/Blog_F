# CS:APP

<!--more-->

{{<admonition tip "简介">}}

本文是CSAPP的读书笔记及问题记录。由于时间、兴趣原因，比较感兴趣的章节未能完成习题，希望能在空闲时将作业及实验补上；例如硬件设计等不感兴趣的章节则进行了大篇幅的略过。

{{</admonition>}}

## Chapter 1

1. clang中stdio.h的主要内容？include的作用?
2. AVX512指令集到底是什么？为什么不同的指令集可以影响程序执行的速度？
3. 什么是操作系统内核(kernel)？
4. 解释性语言和编译性语言之间有什么主要区别？
5. 并发和并行。
   - 线程级并发：CPU同时执行多个控制流，在进程中切换。超线程（hyperthreading）。
   - 指令级并行：同时处理多条指令。流水线（pipelining）、超标量（superscalar）。
   - 单指令、多数据并行：允许一条指令产生多个并行的操作。SIMD。

## Chapter 2

1. 汇编语言、机器语言。
   - 汇编语言是一种低级语言，文本。
   - 机器语言无需翻译计算机即可理解，二进制。

2. 计算机的字长？

   计算机的字长（word size）取决于***数据总线***的宽度，***指明指针数据的标称大小（nominal size）***，限制了虚拟地址的范围。对于总线长度为$w$的计算机，其虚拟地址的范围为$2^w - 1$。

3. C数据类型的典型大小（以字节为单位）。

   | 有符号  |     无符号     | 32位 | 64位 |
   | :-----: | :------------: | :--: | :--: |
   |  char   | unsigned char  |  1   |  1   |
   |  short  | unsigned short |  2   |  2   |
   |   int   |  unsigned int  |  4   |  4   |
   |  long   | unsigned long  |  4   |  8   |
   | int32_t |    uint32_t    |  4   |  4   |
   | int64_t |    uint64_t    |  8   |  8   |
   | char *  |                |  4   |  8   |
   |  float  |                |  4   |  4   |
   | double  |                |  8   |  8   |

   > int32_t和int64_t的出现是为了防止不同编译系统编译相同声明出现数据大小不一的情况。

4. 声明指针。

   对于任何数据类型T，声明T *p，表明p是一个指针对象，指向一个类型为T的对象。

5. 字节存储顺序。

   最低有效位字节在最前面的方式，称为小端法（little endian）；最高有效位字节在最前面的方式，称为大端法（big endian）。比较新的微处理器使用双端法（bi-endian），可以把机器配置为大端或者小端来运行。一旦选择了操作系统，字节顺序随之固定。

6. 为什么相同的程序在不同机器或系统上编译得到的二进制代码不同？既然不同，那为什么exe文件，deb文件，他们的工作原理又是什么？

7. 异或逆元（a^a = 0）:

   ```c
   void inplace_swap(int *x, int *y) {
       *y = *x ^ *y;
       *x = *x ^ *y;
       *y = *x ^ *y;
   }
   ```

8. 移位运算。

   C语言中移位运算不会修改总位数。对于有符号数，几乎所有的编译器/机器的组合都使用算术右移（考虑正负号），对于无符号数，必须使用逻辑右移。

   C语言中移位运算符的优先级小于加减法。

9. 整数隐式转换。

   当执行一个运算时，假设同时存在有符号数和无符号数，那么C会转换有符号数为无符号数，并且假设所有的数都是非负的。*当把short转换为unsigned时，先转换长度，后转换符号，这是C语言标准所要求的。*

**第二章至2.3.2，暂且跳过。**

## Chapter 3

1. 链接器的任务之一就是为函数调用找到匹配函数的可执行代码的位置（地址）。

2. x86-64的CPU包含一组16个存储64位值的通用目的寄存器，用于存放整数数据和指针。

3. 操作数值。

   最常用的寻址模式为比例变址寻址：$Imm(r_b, r_i, s) = M[Imm + R[r_b] + R[r_i] \cdot s]$。

   其他的寻址模式均为该寻址模式的特殊形式。

4. 程序栈栈顶为小地址，栈底为大地址。

5. 条件码寄存器，单个位（1bit），由CPU维护，描述了最近的算术或逻辑操作的属性，可以检测这些寄存器来执行条件分支指令。（程序状态字寄存器PSW、标志寄存器FR）

   - CF：进位标志
   - ZF：零标志
   - SF：符号标志 —— 最近的操作得到的结果为负数。
   - OF：溢出标志 —— 最近的操作导致补码正负溢出。

6. PC中保存的为下一条指令的地址。

7. 实现条件操作的传统方式为通过使用**控制**的**条件跳转**。当条件满足时，程序沿着一条执行路径执行，当条件不满足时，就走另一条路径。这种机制简单而通用，但是在现代处理器上，它可能会非常低效。（分支预测逻辑）

8. 另一种实现条件策略的方式是使用**数据**的**条件传送**。这种方法计算一个条件操作的两种结果，然后再根据条件是否满足选择其中一个。（无需进行分支预测）

9. 不是所有的条件表达式都可以使用条件传送来编译。

10. C语言指针运算。

    int E[some number]

    |    表达式    | 类型  |         值         |
    | :----------: | :---: | :----------------: |
    |      E       | int * |       $x_E$        |
    |     E[0]     |  int  |      M[$x_E$]      |
    |     E[i]     |  int  |    M[$x_E$ + 4]    |
    |    &E[2]     | int * |     $x_E$ + 8      |
    |  E + i  - 1  | int * |   $x_E$ + 4i - 4   |
    | *(E + i - 3) |  int  | M[$x_E$ + 4i - 12] |
    |  &E[i] - E   | long  |         i          |

    可以看到C有一个神奇的特性，就是当两个指针相减时，得到的数据类型为long，值为两地址差除以指针所指数据类型长度。

    ```C
    /* arrptr.c */
    #include <stdio.h>
    
    void main() {
    	int e[10];
    	int i = 2;
    	printf("e: %p\n", e);
    	printf("&e[%d]: %p\n", i, &e[i]);
    	printf("&e[%d] - e: %ld\n", i, &e[i] - e);	// 相减得到数据类型为long，使用%ld输出。如果使用%p编译器会报错。
    }
    ```

    ```bash
    gcc -o arrptr.out arrptr.c	# gcc编译
    ./test.out				# 查看输出
    # 输出
    > e: 0x7fffef9ea770
    > &e[2]: 0x7fffef9ea778
    > &e[2] - e: 2
    ```

    可以看到差并不相等。

11. 数组的嵌套。

    ```C
    int A[5][3];
    
    typedef int row3_t[3];	// 上下两种定义方式等价
    row3_t A[5];
    ```

12. 异质的数据结构。

    C语言提供了两种将不同类型的对象组合到一起创建数据类型的机制：结构（structure），用关键字struct声明，将多个对象集合到一个单位中；联合（union），用关键字union声明，允许用几种不同的类型来引用一个对象。

13. 联合总的大小等于其最大字段的大小。

14. 计算机系统对基本数据类型的合法地址作了一些限制，要求某种类型对象的地址必须是某个值K的倍数。称为数据对齐限制。数据对齐限制简化了接口设计，提高内存系统的性能。**对齐的原则是任何K字节的基本对象的地址必须是K的倍数**。

15. 缓冲区溢出（buffer overflow）。

    C对于数组引用不进行任何边界检查，对越界数组的写操作会破坏存储在运行时栈中的状态信息。

    ```C
    /* echo.c */
    #include <stdio.h>
    
    char *getchars(char *s) {
    	int c;
    	char *dest = s;
    	while ((c = getchar()) != '\n' && c != EOF)
    		*dest++ = c;
    	if (c == EOF && s == dest)
    		return NULL;
    	*dest++ = '\0';
    	return s;
    }
    
    void putchars(char *s) {
    	while (*s != '\0')
    		printf("%c", *s++);
        printf("\n");
    }
    
    void echo() {
    	char buf[1];	/* Way too small! */
    	getchars(buf);
    	putchars(buf);
    }
    
    void main() {
    	echo();
    }
    ```

    ```bash
    gcc -o echo.out echo.c
    ./echo.out
    # Input
    > 123
    # Output
    > 123
    > *** stack smashing detected ***: terminated
    > Aborted (core dumped)
    ```

    > 补记：C语言中单双引号的区别
    >
    > - 单引号表示单个字符，要求字符数量只能为1，表示字符常量
    > - 双引号表示0到多个字符，表示字符串常量

16. 蠕虫和病毒。

    > 蠕虫和病毒都试图在计算机中传播它们自己的代码段
    >
    > - 蠕虫（worm）可以自己运行，并且能够将自己的等效副本传播到其它机器
    > - 病毒（vrius）能将自己添加到包括操作系统在内的其他程序中，但它不能独立运行

## Chapter 4

1. ISA

   一个处理器支持的指令和指令的字节级编码称之为他的指令集体系结构（Instuction-Set Architecture, ISA）。ISA在编译器编写者和处理器设计人员之间提供了一个概念抽象层，编译器编写者只需要知道允许哪些指令，以及它们是如何编码的；而处理器设计者必须建造出执行这些指令的处理器。

2. “复杂指令集计算机”（CISC）&“精简指令集计算机”（RISC）

   |                             CISC                             |                          早期的RISC                          |
   | :----------------------------------------------------------: | :----------------------------------------------------------: |
   |      指令数量很多。Intel描述全套指令的文档有1200多页。       |               指令数量少的多。通常少于100个。                |
   | 有些指令的延迟很长。包括将一整块从内存的一个部分复制到另一部分的指令，以及其他一些将多个寄存器的值复制到内存或从内存复制到多个寄存器的指令。 | 没有较长延迟的指令。有些早期的RISC机器甚至没有整数乘法指令，要求编译器通过一系列加法来实现乘法。 |
   |    编码长度是可变的。x86_64的指令长度可以是1～15个字节。     |      编码是固定长度的。通常所有的指令都编码为4个字节。       |
   | 指定操作数的方式多种多样。x86_64中，内存操作数指示符可以有许多不同的组合，这些组合由偏移量、基址和变址寄存器以及伸缩因子组成。 |           简单寻址方式。通常只有基址和偏移量寻址。           |
   |         可以对内存和寄存器操作数进行算术和逻辑运算。         | 只能对寄存器操作数进行算术运算和逻辑运算。允许使用内存引用的只有load和store指令。load指令是从内存读到寄存器，store是从寄存器写到内存。这种方法称之为load/store结构。 |
   | 对机器级程序来说实现细节是不可见的。ISA提供程序和如何执行程序之间的清晰的抽象。 | 对机器级程序来说实现细节是可见的。有些RISC机器禁止某些特殊的指令序列，而有些跳转需要到下一条指令执行完才会生效。编译器必须在这些约束条件下进行性能优化。 |
   | 有条件码，作为指令执行的副产品，设置了一些特殊的标志位，可以用于条件分支检测。 | 没有条件码。相反，对条件检测来说，要用明确的测试指令，这些指令会将测试结果放在一个普通的寄存器中。 |
   |      栈密集的过程链接。栈被用来存储过程参数和返回地址。      | 寄存器密集的过程链接。寄存器被用来存取过程参数和返回地址。因此有些过程能完全避免内存引用。通常处理器包含更多的寄存器。 |

3. 相邻指令间存在的相关。
   1. 数据相关（data dependency）：下一条指令会用到这一条指令计算的结果。
   2. 控制相关（control dependecny）：一条指令要确定下一条指令的位置，例如跳转、调用或是返回指令时。
4. 冒险是指相关导致流水线产生计算错误。有数据冒险（data hazard）和控制冒险（control hazard）两种形式。
5. 避免数据冒险。
   1. 暂停（stalling）。
   2. 数据转发（data forwarding），简称转发，又称旁路（passby）。
6. 避免控制冒险。控制冒险只发生在ret和跳转指令上。发现错误指令，向错误指令中插入气泡，同时取出正确的指令，称为指令排除（instruction squashing）。
7. 流水线设计的CPI（Cycles Per Instruction，每指令周期）的测量值不可能小于1.0。较新的处理器支持超标量（superscalar）的操作，意味着它们通过并行地取址、译码和执行多条指令，可以实现小于1.0的CPI。

## Chapter5

> 程序员必须写出清晰简洁的代码，这样做不仅是为了自己能够看懂代码，也是为了在检查代码和今后需要修改代码时，其他人能够读懂和理解代码。

1. 妨碍优化的因素

   1. 内存中两个指针指向同一个内存的位置的情况称为内存别名使用（memory aliasing）。内存别名使用是妨碍编译器优化的主要因素。

      ```C
      void twiddle1(long *xp, long *yp) {
          *xp += *yp;
          *xp += *yp;
      }
      
      void twiddle2(long *xp, long *yp) {
          *xp += 2 * *xp;
      }
      ```

      可以看到`twiddle2`函数相较于`twiddle1`函数有着更少次数的内存引用。但是当`*xp`与`*yp`指向相同位置时，两函数会带来不同的结果。 

   2. 函数调用。

      ```C
      long counter = 0;
      
      long f() {
          return counter++;
      }
      
      long func1() {
          return f() + f() + f() + f();
      }
      
      long func2() {
          return 4 * f();
      }
      ```

      可以看到`func2`相较于`func1`有着更少次数的函数调用。但是对于程序中给定的`f()`，由于两函数对`f()`的调用次数不同，因此最后的结果也不同。

2. 循环展开（loop unrolling）技术。在循环中，每次迭代计算两个元素。

   前置和（prefix sum）:
   $$
   p_0 = a_0 \\
   p_i = p_{i - 1} + a_i, 1 \le i < n \\
   $$


   ```C
   /* Compute preifx sum of vector a */
   void psum1(float a[], float p[], long n) {
       long i;
       p[0] = a[0];
       for (i = 1; i < n; i++) {
          	p[i + 1] = p[i] + a[i];
       }
   }
   
   void psum2(float2 a[], float p[], long n) {
       long i;
       p[0] = a[0];
       for (i = 1; i < n - 1; n += 2) {
           float mid_val = p[i - 1] + a[i];
           p[i] = mid_val;
           p[i + 1] = mid_val + a[i + 1];
       }
       /* For even n, finish remaining element */
       if (i < n)
           p[i] = p[i - 1] + a[i];
   }
   ```

   循环展开可以使用更少的循环次数完成计算，减少计算的时间。

3. 最小二乘拟合

   对于一个数据点$(x_1, y_1), (x_2, y_2), ..., (x_n, y_n)$的集合，使用最小二乘拟合，寻找一条形如$y=mx+b$的线，使得下面这个误差度量最小：
   $$
   E(m,b)= \sum_{i=1}^{n}(mx_i+b-y_i)^2
   $$
   将$E(m,b)$分别对$m$和$b$进行求导，把两个函数的导数设为0,进行推导即可的出结果。

4. 结构体的点运算符（.）和箭头运算符（->）。

   点运算符用于结构体对象访问其成员，而箭头运算符用于结构体对象的指针访问其成员。

   ```C
   struct student {
       char Name[10];
       short age;
   } stu, *pstu;
   
   short age = stu.age;
   short age = pstu -> age;
   ```

5. 内存分配函数。

   - malloc

     ```C
     void *malloc(size_t size);
     ```

     参数：

     - size - 元素的大小

     该函数返回一个指针 ，指向已分配大小的内存。如果请求失败，则返回 NULL。

   - calloc

     ```C
     void *calloc(size_t nitems, size_t size);
     ```

     参数：

     - nitems - 要被分配的元素个数
     - size - 元素的大小

     该函数返回一个指针，指向已分配的内存。如果请求失败，则返回 NULL。与malloc之间的不同是，malloc不会设置内存为0，而calloc会设置内存为0。

6. 程序性能提升

   1. 原始代码。

      ```c
      #include <stdlib.h>
      #define IDENT 0
      #define OP +
      
      typedef long data_t;
      
      /* Create abstract data type for vector */
      typedef struct {
      	long len;
      	data_t *data;
      } vec_rec, *vec_ptr;
      
      /* Create vector of specified length */
      vec_ptr new_vec(long len) {
      	/* Allocate header strcuture */
      	vec_ptr result = (vec_ptr) malloc(sizeof(vec_rec));
      	data_t *data = NULL;
      	if (!result) 
      		return NULL;	/* Couldn't allocate storage */
      	result -> len = len;
      	/* Allocate array */
      	if (len > 0) {
      		data = (data_t *) calloc(len, sizeof(data_t));
      		if (!data) {
      			free((void *) result);
      			return NULL;	/* Couldn't allocate storage */
      		}
      	}
      	/* Data will either be NULL or allocated array */
      	result -> data = data;
      	return result;
      }
      
      /*
       * Retrieve vector element and store at dest.
       * Return 0 (out of bound) or 1 (successful)
       */
      int get_vec_element(vec_ptr v, long index, data_t *dest) {
      	if (index < 0 || index >= v -> len)
      		return 0;
      	*dest = v -> data[index];
      	return 1;
      }
      
      /* Return length of vector */
      long vec_length(vec_ptr v) {
      	return v -> len;
      }
      
      /* Implementation with maximum use of data abstraction */
      void combine1(vec_ptr v, data_t *dest) {
      	long i;
      
      	*dest = IDENT;
      	for (i = 0; i < vec_length(v); i++) {
      		data_t val;
      		get_vec_element(v, i, &val);
      		*dest = *dest OP val;
      	}
      }
      ```

   2. 代码移动，消除循环的低效率

      ```C
      /* Move call to vec_length out of loop */
      void combine2(vec_ptr v, data_t *dest) {
      	long i;
      	long length = vec_length(v);
      
      	for (i = 0; i < length; i++) {
      		data_t val;
      		get_vec_element(v, i, &val);
      		*dest = *dest OP val;
      	}
      }
      ```

      将函数调用移出循环，提高循环效率。

      循环中的渐进低效率：

      ``` C
      /* Convert string to lowercase: slow */
      void lower1(char *s) {
          long  i;
          
          for (i = 1; i < strlen(s); i++) {
              if (s[i] >= 'A' && s[i] <= 'Z')
                  s[i] -= ('A' - 'a');
          }
      }
      
      /* Convert string to lowercase: faster */
      void lower2(char *s) {
          long i;
          long len = strlen(s);
          
          for (i = 0; i < len; i++) {
              if (s[i] >= 'A' && s[i] <= 'Z')
                  s[i] -= ('A' - 'z')
          }
      }
      
      /* Sample implementation of library function strlen */
      /* Compute length of string */
      size_t strlen(const char *s) {
          long length = 0;
          while (*s != '\0') {
              s++;
              length++;
          }
          return length;
      }
      
      void mylower(char *s) {
          int dif = 'A' - 'a';
          char *ptr;
          ptr = s;引用
      
          while (*ptr != '\0') {
              if (*ptr >= 'A' && *ptr <= 'Z')
                  *ptr -= dif;
              ptr++;
          }
      }
      ```

   3. 减少过程调用。

      ```C
      data_t *get_vec_start(vec_ptr v) {
          return v -> data;
      }
      
      /* Direct access to vector data */
      void combine3(vec_ptr v, data_t *dest) {
          long i;
          long length = vec_length(v);
          data_t *data = get_vec_start(v);
          
          *dest = IDENT;
          for (i = 0; i < length; i++)
              *dest = *dest OP data[i];
      }
      ```

   4. 消除不必要的内存引用。

      ```C
      /* Accumulate result in local variable */
      void combine4(vec_ptr v, data_t *dest) {
          long i;
          long length = vec_length(v);
          data_t *data = get_vec_start(v);
          data_t acc = IDENT;
          
          for (i = 0; i < length; i++) {
              acc = acc OP data[i];
          }
          *dest = acc;
      }
      ```

   5. 循环展开。

      ```C
      /* 2 x 1 loop unrolling */
      void combine5(vec_ptr v, data_t *dest) {
          long i;
          long length = vec_length(v);
          long limit = length - 1;
          data_t *data = get_vec_start(v);
          data_t acc = IDENT;
          
          /* Combine 2 elements at a time */
          for (i = 0; i < limit; i += 2) {
              acc = (acc OP data[i]) OP data[i + 1];
          }
          
          /* Finish any remaining elements */
          for (; i < length; i++) {
              acc = acc OP data[i];
          }
          *dest = acc;
      }
      ```

   6. 提高并行性。

      ```C
      /* 2 x 2 loop unrolling */
      void combine6(vec_ptr v, data_t *dest) {
          long i;
          long length = vec_length(v);
          long limit = length - 1;
          data_t *data = get_vec_start(v);
          data_t acc0 = IDENT;
          data_t acc1 = IDENT;
          
          /* Combine 2 elements at a tiem */
          for (i = 0; i < limit; i += 2) {
              acc0 = acc0 OP data[i];
              acc1 = acc1 OP data[i + 1];
          }
          
          /* Finish any remaining elements */
          for (; i < length; i++) {
              acc0 = acc0 OP data[i];
          }
          *dest = acc0 OP acc1;
      }
      ```

      提高并行性可以充分利用处理器资源，打破延迟界限。

   7. 重新结合变换。（*不是很理解，猜测是当前循环执行`acc OP`时，可以并行执行下一步的`data[i] OP data[i + 1]？`*）

      ```C
      /* 2 x 1a loop unrolling */
      void combine7(vec_ptr v, data_t *dest) {
          long i;
          long length = vec_length(v);
          long limit = length - 1;
          data_t *data = get_vec_start(v);
          data_t acc = IDENT;
          
          /* Combine 2 elements at a time */
          for (i = 0; i < limit; i += 2) {
              acc = acc OP (data[i] OP data[i + 1]);
          }
          
          /* Finish ant remaining elements */
          for (; i < length; i++) {
              acc = acc OP data[i];
          }
          *dest = acc;
      }
      ```

      与`combine5`不同的是，`combine7`将`(acc OP data[i]) OP data[i + 1]`换成了`acc OP (data[i] OP data[i + 1])`。

7. 程序最大性能的限制。

   - 延迟界限（latency bound）。当一系列操作必须按严格顺序执行时，在下一条指令开始前，当前指令必须结束。当代码中的数据相关限制了处理器利用指令级并行的能力时，延迟界限就会限制程序的性能。
   - 吞吐量界限（throughput bound）。刻画了处理器功能单元的原始计算能力，这个界限是程序性能的终极限制。

8. 周期。

   1. 指令周期：执行一条指令需要消耗的时间，包含了取址和指令执行等时间。由多个机器周期组成。
   2. 机器周期，CPU周期：完成流水线上的一个操作所消耗的时间，通常取内存中读取一个指令字的最短时间规定CPU周期。包含多个时钟周期。
   3. 时钟周期：时钟频率的倒数。

9. 超标量（superscalar）。

   在每个时钟周期（*存疑*）执行多个操作，而且是乱序的（out-of-order）。

   现代微处理器有两个主要部分：指令控制单元（Instruction Control Unit, ICU）和执行单元 (Execution Unit, EU)。前者负责从内存中读出指令，并根据指令序列生成一组针对程序数据的基本操作；而后者主要是执行这些操作。EU接收来自指令控制单元的操作并执行这些操作，通常每个时钟周期会接收多个操作。

10. 优化等级。

    GCC的优化等级主要有-O0、-O1、-O2和-O3四种，随着数字的增大，优化手段更加激进，调试难度不断提升。GCC默认优化等级为-O0，包含非常少量的优化。

    ~~停电了晚饭也没得烧了天还这么热风扇也不转了气死我了~~

11. 使用向量指令达到更高的并行度

    Intel在1999年引入了SSE指令，SSE是Streaming SIMD Extensions的缩写。SSE功能历经几代，最新版本为AVX（Advanced Vector Extension）。SIMD模型使用单条指令对整个向量数据进行操作，这些向量保存在一组特殊的向量寄存器中，可以并行的执行多个加法或乘法。

12. 优化的限制因素。

    1. 寄存器溢出。如果并行度超出了寄存器的数量，那么编译器会将某些临时值放在内存中。

    2. 分支预测和预测错误处罚。在一个使用投机执行（speculative execution）的处理器中，处理器会开始执行预测的分支目标处的指令，如果预测正确，那么处理器就会提交投机执行的指令的结果，如果预测错误，那么就要丢弃所有投机执行的结果。

       1. 不需要过分关心可预测的分支。

       2. 对于不可预测的随即数据，使用数据条件传送的性能往往优于控制条件跳转。

          需要一些测试。在GCC上，生成控制条件跳转的代码风格为：

          ```C
          /* Rearrange two vectors so that for each i, b[i] >= a[i] */
          void minmax1(long a[], long b[], long n) {
              long i;
              for (i = 0; i < n; i++) {
                  if (a[i] > b[i]) {
                      long t = a[i];
                      a[i] = b[i];
                      b[i] = t;
                  }
              }
          }
          ```

          生成条件数据传送的代码风格为：

          ```C
          /* Rearrange two vectors so that for each i, b[i] >= a[i] */
          void minmax2(long a[], long b[], long n) {
              long i;
              for (i = 0; i < n; i++) {
                  long min = a[i] < b[i] ? a[i] : b[i];
                  long max = a[i] < b[i] ? b[i] : a[i];
                  a[i] = min;
                  b[i] = max;
              }
          }
          ```

          需要一些实验，写出函数的不同版本，并检查汇编代码，测试性能。

13. 现代处理器有专门的功能单元来执行加载和存储操作，这些单元有内部的缓冲区来保存未完成的内存操作请求集合。

14. C语言链表结构

    ```C
    typedef struct ELE {
        struct ELE *next;
        long data;
    } list_ele, *list_ptr;
    ```

15. 读/写相关（write/read dependency）。一个内存读的结果依赖于一个最近的内存写。

16. 对于寄存器操作，指令在译码操作的时候，处理器就可以确定哪些指令会影响其他的哪些指令；而对于内存操作，只有到加载和存储地址被计算出来之后，处理器才能确定哪些指令会影响其他的指令。

17. 代码剖析程序（code profiler）。

    Unix系统提供了一个剖析程序GPROF。

    ```bash
    gcc -Og -pg prog.c -o prog.out	# pg意味着程序为剖析而编译和链接
    ./prog.out	# 执行程序，会比正常执行程序稍慢，并产生gmon.out文件
    gprof prog.out	# 调用GPROF来分析gmon.out中的数据
    ```

    除此之外，常用的剖析程序还有Intel的VTUNE和Linux系统通常都有的VALGRIND。

## Chapter 6

> 存储器系统（memory system）是一个具有不同容量、成本和访问时间的存储设备的结构层次。

1. 随机访问存储器（Random-Access Memory，RAM）。RAM断电后所有的信息都会消失。

   1. 静态RAM（SRAM）

      SRAM的工作原理是将位存储在一个双稳态（bistable）的存储器单元里，由于其双稳态特性，只要通电，它就会永远保持它的值，因此被称为静态RAM。

   2. 动态RAM（DRAM）

      DRAM将每个位存储为对一个电容的充电，有很多原因会导致电容的漏电，这就意味着DRAM需要周期性的刷新，因此被称为动态RAM。DRAM存储器单元对干扰十分敏感，暴露在光线下会导致电容改变，数码照相机和摄像机中传感器的本质就是DRAM单元的阵列。

   |  \   | 每位晶体管数 | 相对访问时间 | 是否持续 | 是否对干扰敏感 | 相对花费 |      应用      |
   | :--: | :----------: | :----------: | :------: | :------------: | :------: | :------------: |
   | SRAM |      6       |      1x      |    是    |       否       |  1000x   | 高速缓存存储器 |
   | DRAM |      1       |     10x      |    否    |       是       |    1x    | 主存、帧缓冲区 |

2. DRAM被组织成二维阵列，主要是为了降低芯片上地址引脚的数量，缺点是必须分两步发送地址，增加了访问时间。

3. 非易失性存储器（nonvolatile memory），由于历史性原因，一般统称为只读存储器（Read-Only Memory，RAM）。

4. 固件（firmware）。

   存储在ROM中的程序通常被称为固件，当一个计算机系统通电以后，它会运行存储在ROM中的固件，例如PC的BIOS。

5. 格式化的磁盘容量。

   磁盘控制器必须对磁盘进行格式化，才能在磁盘上存储数据。格式化包括用标识扇区的信息填写扇区之间的空隙，标识出表面有故障的柱面而不使用它们，以及在每个区中预留出一组柱面作为备用。

6. 例如图形卡、监视器、鼠标、键盘和磁盘这样的I/O设备，都是通过I/O总线，例如Intel的外围设备总线（Peripheral Component Interconnect，PCI）总线连接到CPU和主存的。

7. 存储器结构的中心思想是，对于每一个K，位于第K层的更快更小的存储设备作为位于第K+1层的更大更慢的存储设备的缓存。在任何时刻，第K层的缓存包含第K+1层块的一个子集的副本。

8. 缓存写。

   - 写命中（write hit）
     - 直写（write-through），立即将高速缓存块中的内容写进低一层中，缺点是每次写都会引起总线流量。
     - 写回（write-back），当替换算法需要驱逐块时才进行写，缺点是提高了复杂性，需要维护一个额外的修改位（dirty bit）。
   - 写不命中
     - 写分配（write-allocate），加载低一层的块到高速缓存中，缺点是每次不命中都会导致块更新。
     - 非写分配（not-write-allocate），直接写进低一层中。

   直写高速缓存一般搭配非写分配，而写回高速缓存一般搭配写分配。

9. 高速缓存即保存数据，也保存指令。只保存指令的称为i-cache，只保存数据的称为d-cache，既保存数据又保存指令的称为统一的高速缓存 （unified cache）。现代处理器一般包含独立的i-cache和d-cache，这样做使得处理器能够同时读一个指令字和一个数据字；并且i-cache通常是只读的，因此比较简单，可以针对不同的访问模式来优化这两个高速缓存。

10. 


---

> 作者: Blathers  
> https://blathers23.netfily.app/csapp/
