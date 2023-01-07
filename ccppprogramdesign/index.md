# C/C++程序设计

<!--more-->

> 南方科技大学计算机系“C/C++程序设计”课程[视频](https://www.bilibili.com/video/BV1Vf4y1P7pq)。

## WEEK01

---

### Different Programing Languages

1. 编译型语言&解释型语言：

   - 编译型语言程序在执行前需要一个专门的编译过程，把程序编译为机器语言的文件，运行时不需要重新翻译，直接使用编译的结果。

   - 解释性语言编写的程序不进行预先编译，以文本的方式存储程序代码。在程序运行的时候，解释性语言必须先解释再运行。

2. Advantages of C/C++:

   - High efficiency
   - Widely optimized compilers
   - Access memory directly
   - Excellent on computing

### Compile and Line

1. function prototypes(声明) & definitiones:

   - function prototypes normally are put into head files (*.h, *.hpp)

     ```c++
     int mul(int a, int b);
     ```

   - function definitiones normally are in source files (*.c, *.cpp)

     ```c++
     int mul(int a, int b) {
         return a * b;
     }
     ```

2. Separate the sorce code into multiple files:

   ```C++
   // main.cpp
   #include <iostream>
   #include "mul.hpp"
   
   using namespace std;
   int main() {
       int a, b;
       int result;
       
       cout << "Pick two integers:";
       cin >> a;
       cin >> b;
       
       result = mul(a, b);
       cout << "The result is " << result << endl;
       
       return 0;
   }
   ```

   ```C++
   // mul.hpp
   #pragma once
   int mul(int a, int b);
   ```

   ```c++
   // mul.cpp
   #include "mul.hpp"
   int mul(int a, int b) {
       return a * b;
   }
   ```

   要生成可执行文件，应当执行以下命令：

   ```sh
   # 对源文件进行编译
   g++ -c main.cpp	# 生成目标文件mian.o
   g++ -c mul.cpp	# 生成目标文件mul.o
   # 对目标文件进行链接
   g++ main.o mul.o -o mul	# 生成可执行文件mul
   ```

3. Debug

   1. Compilation errors
      - Normally caused by grammar error
      - Check the source code
   2. Link errors
      - "Symbol not found"
      - Function misspelled
   3. Runtime errors
      - The source code can successfully compiled and linked
      - The floating point exception (divided by 0) will kill the program
      - It is a typical runtime error

### Preprocessor and Macros(宏)

1. Preprocessor

   - The preprocessor is excuted before the compilation

   - Preprocessing directives(预处理指令) begin with a # character

   - Each directive occupies one line

   - Preprocessing instruction

     (define, undef, include, if, ifdef, ifndef, else, elif, endif, line, error, pragma)

2. Include directive

   原文件：

   ```c++
   #include "mul.hpp"
   int main(){}
   ```

   经过预处理器预处理得到：

   ```c++
   int mul(int a, int b);
   int main(){}
   ```

3. Macros(#define)

   原文件：

   ```c++
   #define PI 3.14
   double len(double r) {
       return 2.0 * PI * r;
   }
   ```

   经过预处理器预处理得到：

   ```c++
   double len(double r) {
       return 2.0 * 3.14 * r;
   }
   ```

### Simple Output and Input

1. C++ Style Output

   - What is `cout`?

     ```c++
     std::ostream cout;
     ```

   - `cout` is an object of data type ostream in namespace std.

     ```c++
     cout << "hello" << endl;
     ```

   - `<<` is an operator which is defined as follows

     ```c++
     basic_ostream& operator<<( short value );
     ...
     ```

   - `endl`, an output-only I/O manipulator. It will output a new line character and flushes.

2. C++ Style Input

   - Similarly, cin is an object of type `std::istream`
   - `>>` is an operator

3. C Style Output

   ```c
   int v = 100;
   printf("Hello, value = %d\n", v);
   ```

   - `int printf( const char *format, ... );` is a function 
   - format: a string specifying how to interpret the data
   - `%d` will let the function interpret v as an integer

4. C Style Input

   ```c
   int v;
   int ret = scanf("%d", &v);
   ```

   - scanf reads data from stdin, and interrpret the input as an integer and store it into v

5. Command line arguments

   ```c++
   #include <iostream>
   
   using namespace std;
   int main(int argc, char ** argv) {
       for (int i = 0; i< argc; i++) {
           cout << i << ": " << argv[i] << endl;
       }
       return 0;
   }
   ```

   argc是arugment count的缩写，表示传入main函数的参数个数。argv是argument vector的缩写，表示传入main函数的参数序列或指针，并且第一个参数`argv[0]`是包含路径的程序名称。

## WEEK02

---

### Integer Numbers

1. int

   - `int` is the most frequently used integer type

2. Initialization

   - Uninitialized variables may have random values. The behavior depends on the compiler. 

     ```c++
     #include <iostream>
     using namespace std;
     int main() {
         int num1;
         int num2;
         cout << "num1 = " << num1 << endl;
         cout << "num2 = " << num2 << endl;
     
         return 0;
     }
     
     // num1 = 32767
     // num2 = -137238492
     ```

     Clang(x86_64)表现为随机值，Clang(arm64)表现为0 

   - How to initialize

     ```c++
     int num;
     num = 10;
     
     int num = 10;
     
     int num (10);
     
     int num {10}
     ```

3. Overflow

   ```c++
   int a = 789789;
   int b = 789987;
   int c = a * b;
   cout << "c = " << c << endl;
   
   // c = -1069976775
   ```

   The correct result is 0xC0397339, the sign bit is 1

4. signed and unsigned

   - `unsigned`没有符号位，32位全部保存正数。
   - `signed int` can be shorten as `int`. Its range is [-2^31, 2^31 - 1] if it's 32-bit
   - `unsigned int`: Its range is [0, 2^32 - 1] if it's 32-bit

5. Different Data Types for Integer

   - use `long int` for longer integers
   - use `short int` for shorter integers
   - and `long long`
   - C and C++ standards do not fix the widths of them
   - `sizeof` opertor can return the width in bytes.

6. sizeof

   - It is a operator, not a function

   ```c++
   #include <iostream>
   
   using namespace std;
   
   int main() {
       int i = 0;
       short s = 0;
   
       cout << "sizeof(int) = " << sizeof(int) << endl;
       cout << "sizeof(i) = " << sizeof(i) << endl;
       cout << "sizeof(short) = " << sizeof(short) << endl;
       cout << "sizeof(long) = " << sizeof(long) << endl;
       cout << "sizeof(size_t) = " << sizeof(size_t) << endl;
   }
   
   // sizeof(int) = 4	// 长度为4个字节
   // sizeof(i) = 4
   // sizeof(short) = 2
   // sizeof(long) = 8
   // sizeof(size_t) = 8
   ```

   函数的参数必须是一个变量，但是`sizeof(int)`中的`int`不是变量，因此`sizeof`是一个操作符

### More Integer Types

1. char

   - char: type for character, 8-bit integer indeed
   - signed char: signed 8-bit integer
   - unsigned char: unsigned 8-bit integer
   - char: either signed char or unsignede char

2. Integers and characters

   - How we represent a character?
     - use an 8-bit integer
     - use an 16-bit or 32-bit integer (Chinese characters)

   ```C++
   #include <iostream>
   
   using namespace std;
   
   int main() {
       char c1 = 'C';
       char c2 = 80;
       char c3 = 0x50;
       char16_t c4 = u'于';
       char32_t c5 = U'于';
       cout << c1 << ":" << c2 << ":" << c3 << endl;
       // 添加+使c1、c2、c3以整数的形式打印出来
       cout << +c1 << ":" << +c2 << ":" << +c3 << endl;
       cout << c4 << endl;
       cout << c5 <<endl;
   }
   
   // C:P:P
   // 67:80:80
   // 20110
   // 20110
   ```

3. bool

   - A C++ keyword, but not a C keyword
   - bool width: 1 byte (8 bit), NOT 1 bit
   - Value: true (1) or false (0)

   ```C++ 
   #include <iostream>
   
   using namespace std;
   
   int main() {
       bool b1 = true;
       int i = b1;
       bool b2 = -256;	// bool类型不为零均为true
       cout << "i = " << i << endl;
       cout << "b1 = " << b1 << endl;
       cout << "b2 = " << b2 << endl;
   }
   
   // i = 1
   // b1 = 1
   // b2 = 1
   ```

4. Boolean in C

   - Use typedef to create a type

     ```c
     typedef char bool;
     #define true 1
     #define false 0
     ```

   - Defined in stdbool.h since C99

     ```c
     #include <stdbool.h>
     ```

5. size_t

   - Computer memory keeps increasing
   - 32-bit int was enough in the past to for data length
   - But now it is not

   `size_t`:

   - Unsigned integer
   - Type of the result of `sizeof` operator
   - Can store the maximum size of theoretically possible object of any type
   - 32-bit or 64-bit

6. Fixed width integer types(since C++11)

   Defined in `<cstdint>`:

   - `int8_t`, `int16_t`, `int32_t`, `int64_t`, `uint8_t`, `uint16_t`, `uint32_t`, `uint64_t`, ...
   - and some useful macros: `INT8_MIN`, `INT16_MIN`, `INT32_MIN`, `INT64_MIN`, `INT8_MAX`, `INT16_MAX`, `INT32_MAX`, `INT64_MAX`, ...

### Floating-point Numbers

1. float

   ```C++
   #include <iostream>
   #include <iomanip>
   
   using namespace std;
   
   int main() {
       float f1 = 1.2f;    // f意味该数为float型，否则为double
       float f2 = f1 * 1e15;
       cout << fixed << setprecision(15) << f1 << endl;    // setprecision设定有效位
       cout << fixed << setprecision(15) << f2 << endl;
       return 0;
   }
   
   // 1.200000047683716
   // 1200000038076416.000000000000000
   ```

   - You want 1.2, but `float` can only provide you 1.200000047683716...

   - Floating-point operations always bring some tiny errors, those errors cannot be eliminated
   - What can we do: to manage them not to cause a problem

2. Floating-point types

   - `float`: single precision floating-point type, 32 bits
   - `double`: double precision floating-point type, 64 bits
   - `long double`: extended precision floating-point type
     - 128 bits if supported
     - 64 bits otherwise
   - half precision floating-point, 16 bits
     - used in deep learning, but not a C++ standard

3. Floating-point VS integer

   - Represent values between integers
   - A much greater range of values 
   - Floating-point operations are slower than integer operations
   - Lose precision
   - `double` operations is slower than `float`

4. Precision

   ```C++
   #include <iostream>
   
   using namespace std;
   
   int main() {
       float f1 = 2.34e10f;
       float f2 = f1 + 10;
   	
       // fixed-point定点数，小数点后位数固定
       cout.setf(ios_base::fixed, ios_base::floatfield);   
       cout << "f1 = " << f1 << endl;
       cout << "f2 = " << f2 << endl;
       cout << "f1 - f2 = " << f1 - f2 << endl;
       cout << "(f1 - f2 == 0) = " << (f1 - f2 == 0) << endl;
   
       return 0;
   }
   
   // f1 = 23399999488.000000
   // f2 = 23399999488.000000
   // f1 - f2 = 0.000000
   // (f1 - f2 == 0) = 1
   ```

   Can we use == operator to compare two floating point numbers?

   ```c++
   if (f1 == f2)	// bad
   if (fabs(f1 - f2) < FLT_EPSILON)	// good
   ```

5. `inf` and `nan`

   - `inf`: infinity
   - `nan`: not a number

### Arithmetic Operators(算术运算)

1. Constant numer

   ```c++
   95	// decimal
   0137	// octal 前加0表示8进制
   0x5f	// hexadecimal 前加0x表示16进制
       
   95	// int
   95u	// unsigned int
   95l	// long
   95ul	// unsigned long
   95lu	// unsigned long
       
   3.14159	// 3.14159
   6.02e23	// 6.02 * 10^23
   1.6e-19	// 1.6 * 10^-19
   3.0	// 3.0
       
   6.02e23L	// long double
   6.02e23f	// float
   6.02e23	// double
   ```

2. Const type qualifier(const 限定符)

   ```C++
   const float PI = 3.1415926f;
   PI += 1;	// error
   ```

   - If a variable/object is constant-qualified, it cannot be modified
   - It must be initialized when you define it

3. auto

   `auto` is placeholder type specifier

   The type of variable will be deduced from its initializer

   ```C++
   auto a = 2;	// type of a is int
   auto b = 2.3;	// type of b is double
   auto c;	// valid in C, error in C++
   auto d = a * 1.2;	// type of d is double
   
   a = 2.3;	// 2.3 will converted to a int 2, then assigned to a
   ```

4. Arithmetic operators

   | Operator name       | Synatax |
   | ------------------- | ------- |
   | unary plus          | +a      |
   | unary minus         | -a      |
   | addition            | a + b   |
   | subtraction         | a - b   |
   | multiplication      | a * b   |
   | division            | a / b   |
   | modulo(求余)        | a % b   |
   | bitwise NOT         | ~a      |
   | bitwise AND         | a & b   |
   | bitwise OR          | a \| b  |
   | bitwise XOR         | a ^ b   |
   | bitwise left shift  | a << b  |
   | bitwise right shift | a >> b  |

   Operator Precdence(优先级):

   - `a++`
   - `++a`
   - `* /`
   - `+ -`
   - `<< >>`

5. Data type conversions

   ```C++
   int num_int1 = 9;	// assigning an int value to num_int1
   int num_int2 = 'C';	// implicit conversion(隐式转换)
   int num_int3 = (int)'C';	// explicit conversion, C-style
   int num_int4 = int('C');	// explicit conversion, function style
   int num_int5 = 2.8;	// implicit conversion
   float num_float = 2.3;	// implicit conversion from double to float
   short num_short = 650000;	// 低位截断
   ```

   - The source code can be compiled successfully even with warning message when the data types do not match.
   - Please ues expilcit conversion if possible

6. Data loss

   ```C++
   #include <iostream>
   
   using namespace std;
   
   int main() {
       int num_int1 = 0x7ABCDEF0;
       float num_int_float = num_int1;
       int num_int2 = (int)num_int_float;
   
       cout << "num_int1 = " << num_int1 << endl;
       cout << "num_int_float = " << num_int_float << endl;
       cout << "num_int2 = " << num_int2 << endl;
   
       return 0;
   }
   
   // num_int1 = 2059198192
   // num_int_float = 2.0592e+09
   // num_int2 = 2059198208
   ```

7. Division

   - Both operands(操作数) are integers

     - Perform integer division
     - Any fractional part of the answer is discarded to make the result an integer

     ```C++
     float f = 17 / 5;	// f will be 3.f, not 3.4f
     ```

   - One or both operands are floating-point numbers

     - Perform floating-point division

     ```C++
     float f = 17 / 5.f;	// f will be 3.4f
     ```

8. Distinct Operations for Different Types

   - `int`, `long`, `float`, `double`: four kinds of operations

   - If the operands are not the four types, automatic convert their types

     ```C++
     unsigned char a = 255;
     unsigned char b = 1;
     int c = a + b;	// c = 256;
     ```

   - The  operands will be converted to one of the four types without losing data

## WEEK03

---

### `if` Statement

1. Statements are executed conditionally

   - if

   ```C++
   int num = 10;
   if (num < 5) {
       cout << "The number is less than 5. " << endl;
   }
   ```

   - if-else

   ```C++
   if (num == 5) {
       cout << "The number is 5. " << endl;
   } else {
       cout << "The number is not 5. " << endl;
   }
   ```

   - if-else-if-else

   ```C++
   if (num < 5) {
       cout << "The number is less than 5." << endl;
   } else if (num > 10) {
       cout << "The number is greater than 5." << endl;
   } else {
       cout << "The number is in range [5, 10]. " << endl;
   }
   ```

2. A little more complex

   ```C++
   if (num < 10)
   if (num < 5)
   cout << "The number is less than 5" << endl;
   else
   // else 与最近的if进行匹配
   cout << "The number is less than 10 and greater than 5" << endl;
   ```

3. `? : `operator

   ```C++
   bool isPositive = true;
   int factor = 0;
   
   if (isPositive) {
       factor = 1;
   } else {
       factor = -1;
   }
   ```

   ```C++
   factor = isPositive ? 1 : -1;
   ```

   ```C++
   facotr = isPositive * 2 - 1;
   ```

### Condition

1. The condition should be an expression which is convertilbe to `bool`

   - Its value can be `bool`, `int`, `char`, `float`

2. Relational Expressions

   - The condition can be a relational expression

   - The 6 relational/comparison operators

     | Operator name             | Example |
     | ------------------------- | ------- |
     | equal to                  | a == b  |
     | not equal to              | a != b  |
     | less than                 | a < b   |
     | greater than              | a > b   |
     | less than and equal to    | a <= b  |
     | greater than and equal to | a >= b  |

   - Return 1 if condition is true

   - Return 0 if condition is false

3. Logical Expressions

   - If an operand is not `bool`, it will be converted to `tool` implicitly

     | Operator name | Symbol-like operator | Keyword like operator | Example |
     | :-----------: | :------------------: | :-------------------: | :----------: |
     |   Negation    |          !           |          not          |      !a      |
     |      AND      |          &&          |          and          |    a && b    |
     | Inclusive OR  |         \|\|         |          or           |   a \|\| b   |

   - Precedence: ! > && > ||

4. Non-Boolean Expressions

   - They will be converted to `bool` implicitly if it is feasible.

     ```C++
     float count = 0.2f;
     // not recommend to use a floating-point number
     if (count) {	// if (count != 0)
         cout << "There are some. " << endl;
     }
     ```

   - Pointers are also frequently used as conditions

     ```C++
     int* p = new int[1024];
     if (!p) {	// if (p == NULL);
         cout << "Memory allocation failed. " << endl;
     }
     ```

### `while` loop

1. Syntax:

   ```C++
   while (expression) {
       //...
   }
   ```

   If the condition is true, the statement (loop body) will be executed.

2. `do-while` loop

   - The test takes place after iteration in a `do-while` loop

   ```C++
   int num = 10;
   do {
       cout << "num = " << num << endl;
       num--;
   } while (num > 0);
   ```

3. `break` statement

   - Terminate a loop

   ```C++
   int num = 10;
   while (num > 10) {
       if (num == 5) {
           break;
       }
       cout << "num = " << num << endl;
       num--;
   }
   ```

4. `continue` statement

   - skip the remianing part of the loop body and continue the next iteration

   ```C++
   int num = 10;
   while (num > 10) {
       if (num == 5) {
           num--;
           continue;
       }
       cout << "num = " << num << endl;
       num--;
   }
   ```

5. The Condition, Be Careful!

   - Expression `3 + 4` has a value;

   - Expression `a + b` has a value;

   - Expression `(a == b)` has value (true or false);

   - `a = b` is an assignment, also an expression and has a value (b);

### `for` loop

1. Syntax

   ```C++
   for (init-clause; cond-expression; iteration-expression) {
       loop-statement;
   }
   
   // Example
   init sum = 0;
   for (int i = 0; i < 10; i++) {
       sum += i;
       cout << "Line " << i << endl;
   }
   cout << "sum = " << sum << endl;
   ```

2. Endless Loop

   ```C++
   for (;;) {
       cout << "Endless Loop!" << endl;
   }
   
   while (true) {
       cout << "Endless Loop!" << endl;
   }
   ```

### `goto` and `switch` Statement

1. `goto` Statement

   - Jump to a desired location
   - An unrecommended statement

   ```C++
   float mysquare(float value) {
       float result = 0.0f;
       
       if (value >= 1.0f || value <= 0) {
           cerr << "The input is out of range." << endl;
           goto EXIT_ERROR;
       }
       
       result = value * value;
       return result;
       
       EXIT_ERROR:
       	// do sth such as closing files here
       	return 0.0f;
   }
   ```

2. `switch` Statement

   - Execute one of several statements, depending on the value of a condition
   - `break` prevent to excute some following statement
   - More similar with `goto`, not `if-else`

   ```C++
   switch (input_char) {
       case 'a':
       case 'A':
           cout << "Move left." << endl;
           break;
       case 'd':
       case 'D':
           cout << "Move right." << endl;
           break;
       default:
           cout << "Undifined key." << endl;
           break;
   }
   ```

## WEEK04

---

### Array

1. Arrays

   - A contiguously(连续的) allocated memory

   - Fixed number of object (The array size cannot be changed)

   - Its element type can be any fundamental type (int, float, bool, etc), structure, class, pointer, enumeration(列举)

   ```C++
   int num_array1[5];	// uninitialized array, random values
   int num_array2[5] = {0, 1, 2, 3, 4};	// initialization
   ```

2. Variable-length arrays

   - If the length is not an integer constant expression, the array will  be a variable-length one.
   - 数组的长度在编译时不能确定，只有在运行的时候才可以确定

   ```C++
   int len = 1;
   while (len < 10) {
       int num_array2[len];	// 变长数组不支持初始化
       cout << "len = " << len;
       cout << ", sizeof(num_array2) = " << sizeof(num_array2) << endl;
       len++;
   }
   ```

3. Array of unknown size

   - The number is not specified in the declaration

   ```C++
   int num_array[] = {1, 2, 3, 4};
   ```

   - The arguments of a function

   ```C++
   float array_sum(float values[], size_t length);
   float array_sum(float *values, size_t length);
   ```

4. Element accesssing

   ```C++
   int array1[4] = {9, 8, 7, 6};
   int array2[4];
   array2 = array1;	// error
   // array的实质是数组的首地址
   array2[0] = array1[0];
   array2[1] = array1[1];
   array2[2] = array1[2];
   array2[3] = array1[3];
   ```

   - **No bounds-checking in C/C++**
   - Arrays are not objects in C/C++, can be regarded as addresses

5. Multidimensional arrays

   ```C++
   int mat[2][3] = {{11, 12, 13}, {14, 15, 16}};
   
   for (int r = 0; r < rows; r++) {
       for (int c = 0; c < clos; c++) {
           cout << mat[r][c] << ",";
       }
       cout << endl;
   }
   
   // Arrays of unknown bound
   void init_2d_array(float mat[][], size_t rows, size_t cols);	// error
   void init_2d_array(float mat[][3], size_t rows, size_t cols);
   ```

6. `const` Arrays

   ```C++
   const float PI = 3.14f;
   PI += 1.f;	// error
   const float values[4] = {1.0f, 2.0f 3.0f, 4.0f};
   values[0] = 1.1f;	// error
   
   // Used as function arguments
   float array_sum(const float values[], size_t length) {
       float sum = 0.0f;
       for (int i = 0; i < length; i++) {
           sum += values[i];
           // values[i] = 0;	// error
       }
       return sum;
   }
   ```

### Strings

1. Array-style strings

   - An array-style string (null-terminated string/arrays of characters) is a series of characters stored in bytes in memory
   - This kind of string can be declared as follows

   ```C++
   char rabbit[16] = {'P', 'e', 't',  'e', 'r'};
   char bad_pig[5] = {'P', 'e', 'p', 'p', 'a'};	// a bad one
   char good_pig[6] = {'P', 'e', 'p', 'p', 'a', '\0'};
   ```

   ```C++
   // Returns the number of characters, the first NULL will not be included.
   size_t strlen(const char* str);
   
   char name[10] = {'Y', 'u', '\0', 'S', '.', '0'};
   cout << strlen(name) << endl;
   
   // 2
   ```

2. String literals(常量字符串)

   - It isn't convenient to initial a string character by character.
   - String literals can help

   ```C++
   char name1[] = "Southern University of Science and Technology";
   char name2[] = "Southern University of " "Science and Technology";	// 与第一行没有差别，写法为两个字符串的连接
   char name3[] = "ABCD";
   
   const wchar_t s5[] = L"ABCD";
   const char16_t s9[] = u"ABCD";	// since C++11
   const char32_t s6[] = U"ABCD";	// since C++11
   ```

3. String manipulation(操作) and examination

   - Copy

   ```C++
   char* strcpy(char* dest, const char* src);
   // safe one
   char* strncpy(char* dest, const char* src, size_t count);
   ```

   - Concatenate: appends a copy of src to dest

   ```C++
   char* strcat(char* dest, const char* src);
   ```

   - Compare
     - 如果返回值小于 0，则表示 lhs 小于 rhs。
     - 如果返回值大于 0，则表示 lhs 大于 rhs。
     - 如果返回值等于 0，则表示 lhs 等于 rhs。

   ```C++
   int strcmp(const char *lhs, const char *rsh);
   ```

4. string class

   - Null-terminated string are easy to be out of bound, and to cause problems
   - string class provides functions to manipulate and examinate strings

   ```c++
   std::string str1 = "Hello";
   std::string str2 = "SUSTech";
   std::string result = str1 + ", " + str2;
   
   cout << "result = " + result << endl;
   cout << "The length is " << result.length() << endl;
   cout << "str1 < str2 is " << (str1 < str2) << endl;
   // cout << "strcmp(str1, str2) = " << strcmp(str1, str2) << endl;
   ```

   - Different types of string

   ```C++
   std::string
   std::wstring
   std::u8string	// C++20
   std::u16string	// C++11
   std::u32string	// C++11
   ```

   - String类同样没有越界检查

### Structures, Unions and Enumerations



---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/ccppprogramdesign/  

