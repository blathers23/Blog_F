# C/C++程序设计

<!--more-->

> 《C/C++程序设计》课程[视频](https://www.bilibili.com/video/BV1Vf4y1P7pq)。

{{<center-quote>}}

当你做一件事情很笨，很啰嗦的时候，你大概率做错了。

{{</center-quote>}}

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

### Compile and Link

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
   int main(int argc, char** argv) {
       for (int i = 0; i < argc; i++) {
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

### Structures, Unions and Enumerations(枚举类型)

1. `struct`

   - A `struct` is a type consisting of a sequence of members
   - The members are allocated in an ordered sequence

   ```C
   struct Student {
       char name[4];
       int born;
       bool male;
   };
   
   struct Student stu;
   strcpy(stu.name, "Yu");
   stu.born = 2000;
   stu.male = true;
   
   struct Student stu = {"Yu", 2000, true};
   
   struct Student students[100];
   students[50].born = 2000;
   ```

2. Structure padding

   - In order to align the data in memory, some empty bytes will be padded

   ```C++
   struct Student1 {
       int id;
       bool male;
       char label;
       float height;
   }
   
   struct Student2 {
       int id;
       bool male;
       float height;
       char label;
   }
   
   // size of student1: 12
   // size of student2: 16
   ```

3. `struct` in C and C++

   - `struct` and `class` in C++ are identical(相同) expect for several features
   - No `typedef` needed in C++

4. `union`

   - `union` declaration is similar to `struct` declaration
   - To storage of members overlaps/shared

   ```C++
   union ipv4address{
       std::uint32_t address32;
       std::uint8_t address8[4];
   };
   // sizeof(union ipv4address) is 4
   // 成员首地址变量相同
   
   union ipv4address  ip;
   ip.address8[3] = 127;
   ip.address8[2] = 0;
   ip.address8[1] = 0;
   ip.address8[0] = 1;
   
   cout << "The address is ";
   cout << +ip.address8[3] << ".";
   cout << +ip.address8[2] << ".";
   cout << +ip.address8[1] << ".";
   cout << +ip.address8[0] << endl;
   
   cout << std::hex;
   cout << "in hex" << ip.address32 << endl;
   // The address is 127.0.0.1
   // in hex 7f000001
   ```

5. `enum`

   - `enum` makes a new type
   - It provides an alternative to `const` for creating symbolic constants
   - Its members are integers, but they cannot be operands in arithmetic expressions

   ```C++
   enum color {WHITE, BLACK, RED, GREEN, BLUE, YELLOW, NUM_COLORS};
   enum color pen_color = RED;
   pen_color = color(3);
   
   cout << "We have " << NUM_COLORS << " pens." << endl;	
   // We have 6 pens.
   
   // pen_color += 1;	// error
   int color_index = pen_color;
   color_index += 1;
   cout << "color_index = " << color_index << endl;
   ```

6. An example with struct, union and enum

   ```C
   enum datatype {TYPE_INT8 = 1, TYPE_INT16 = 2, TYPE_INT32 = 4, TYPE_INIT64 = 8};
   
   struct Point {
       enum datatype type;
       union {
           std::int8_t data8[3];
           std::int16_t data16[3];
           std::int32_t data32[3];
           std::int64_t data64[3];
       };
   };
   
   size_t datawidth(struct Point pt) {
       return (size_t)pt.type * 3;
   }
   
   int64_t l1norm(struct Point pt) {
       int64_t result = 0;
       switch(pt.type) {
           case (TYPE_INT8):
               result = abs(pt.data8[0]) + abs(pt.data8[1]) + abs(pt.data8[2]);
               break;
   		...
       }
   }
   
   // declaration and initialization
   struct Point point1 = {.type = TYPE_INT8, .data8 = {-2, 3, 4}};
   ```

### typedef

- `typedef` can create an alias(别名) for a type
- It can be used to replace a possibly complex type name

```C
typedef int myint;
typedef unsigned char vec3b[3];
typedef struct _rgb_struct {
    unsigned char r;
    unsigned char g;
    unsigned char b;
} rgb_struct;
```

## WEEK05

---

### Pointers

- A pointer is declared like a variable, but with * after the type
- What stored in a pointer variable is an address
- Operator `&` can take the address of an object or a variable of fundamental types
- Operator `*` can take the content that the pointer point to

```C
int num = 10;
int *p1 = NULL, *p2 = NULL;
p1 = &num;
p2 = &num;
*p1 = 20;
*p2 = 30;
```

1. Struct member accessing

   - P -> member(*p).member

   ```C++
   struct Student {
       char name[4];
       int born;
       bool male;
   }
   
   Student stu = {"Yu", 2000, true};
   Student *pStu = &stu;
   
   strncopy(pStu -> name, "Li", 4);
   ```

2. Print out the address

   - Since the value of a pointer is an address,  we can print it out

   ```C
   printf("Address of stu: %p\n", pStu);	// C style
   cout << "Address of stu: " << pStu << endl;	// C++ style
   cout << "Address of stu: " << &stu << endl;
   cout << "Address of member name: " << &(pStu -> name) << endl;
   ```

3. Pointers of Pointers

   - Pointers are variables, they also have address

   ```C
   int num = 10;
   int *p = &num;
   int **pp = &p;
   
   **pp = 5;
   cout << num << endl;	// 5
   ```

4. Constant pointers

   ```C
   int num = 1;
   int another = 2;
   // You cannot change the value the p1 points to through p1
   const int* p1 = &num;
   *p1 = 3;	// error
   num = 3;	// ok
   
   // You cannot change value of p2 (address)
   int* const p2 = &num;
   *p2 = 3;	// okay
   p2 = &another;	// error
   
   // You cannot change either of them
   const int* const p3 = &num;
   ```

   ```C
   int foo(const char *p) {
       // the value that p points to cannot be changed
       char *p2 = p;	
       // syntax error
       // 不可以将一个 const 指针赋值给一个普通指针
   }
   ```


### Pointers and Arrays

1. Use `&` operator to get the addresses of elements

   ```C
   int nums[128];
   int* p0 = &nums[0];
   int* p1 = &nums[1];
   
   printf("p0 = %p\n", p0);
   printf("p1 = %p\n", p1);
   
   // the same behavior
   num[1] = 10;
   *p1 = 10;
   ```

   You can consider an array name as a pointer

   ```c
   int nums[128];
   
   printf("&nums = %p\n", &nums);
   printf("nums = %p\n", nums);
   printf("&nums[0] = %p\n", &nums[0]);
   ```

2. Pointer arithmetic(算术操作)

   - `p + num` or `num + p` points to the num-th element of the type p
   - `p - num` points to the -num-th element

   ```C++
   int nums[4] = {0, 1, 2, 3};
   
   int* p = nums + 1;	// *p == 1
   p++; 	// *p == 2
   // p[1] == 3
   ```

   - Be careful of out-of-bound

3. Differences between a pointer and an array

   - **Array is a constant pointer**
   - The total size of all elements in an array can be got by operator `sizeof`
   - `sizeof` operator to a pointer will  return the size of the address (4 or 8)

   ```C++
   int nums[4] = {0, 1, 2, 3};
   int* p = nums;
   cout << sizeof(nums) << endl;	// 4 * sizeof(int)
   cout << sizeof(p) << endl;	// 4 or 8
   cout << sizeof(double*) << endl;	// 4 or 8
   ```

### Allocate memory: C style

1. Program memory

   The address space of a program contains several data segments

   - Code: executable code
   - Data: initialized static variables
   - BSS: uninitialized static data including variables and constants
   - Heap: dynamically allocated memory
   - Stack: local variables, call stack

2. Memoy allocation

   *与静态申请内存的方式相比，动态申请内存可以获得更大的内存空间，例如申请一个非常大的数组空间，使用 `int nums[SIZE]` 的方式出现内存不足的情况时，换成 `int* nums = malloc(sizeof(int) * SIZE)` 可能可以解决。*

   - Allocate size bytes of uninitialized storage

   ```C
   void* malloc(size_t size)
   ```

3. Memory deallocation

   - The dynamically allocated memory must be deallocated explicitly(显式)

   ```C
   void foo() {
       int* p = malloc(sizeof(int));
       return;
   }	// memoy leak
   ```

   Memory leak: No variable to keep the first address. The management system will not deallocate it automatically. Waste of memory.

### Allocate memory: C++ style

1. Operator `new` and `new[]`

   - Operator `new`  is similar with `malloc()` but with more features

   ```C++
   // allocate an int, default initializer (do nothing)
   int* p1 = new int;
   // allocate an int, initilized to 0
   int* p2 = new int();
   // allocate an int, initilized to 5
   int* p3 = new int(5);
   // allocate an int, initilized to 0
   int* p4 = new int{};
   // allocate an int, initilized to 5
   int* p5 = new int{5};
   
   // allocate a Student object, default initilizer
   Student* ps1 = new Student;
   // allocate a Student object, initialize the members
   Student* ps2 = new Student{"Yu", 2020, 1};
   
   // allocate 16 int, default initializer (do nothing)
   int* pa1 = new int[16];
   // allocate 16 int, zero initialized
   int* pa2 = new int[16]();
   // allocate 16 int, zero initialized
   int* pa3 = new int[16]{};
   // allocate 16 int, the first 3 element are initialized to 1,2,3, the rest 0
   int* pa3 = new int[16]{1, 2, 3};
   
   // allocate memory for 16 Student objects, default initialize
   Student* psa1 = new Student[16];
   // allocate memory for 16 Student objects, the first two are initilized
   Student* psa2 = new Student[16]{{"Li", 2000, 1}, {"Yu", 2001, 1}};
   ```

2. Operator `delete` and `delete[]`

   - Destroys object/objects allocated by `new` and free memory

   ```C++
   // deallocate memory
   delete p1;
   
   // deallocate the memory of the array
   delete pa1;
   // deallocate the memory of the array
   delete []pa2;
   
   // deallocate the memory of the array, and call the destructor of the first element
   delete psa1;
   // deallocate the memory of the array, and call the destructors of all the elements
   delete []psa2;
   ```

## WEEK06

---

### Functions

1. the pointer should be checked first

   ``` C++
   float matrix_max(struct Matrix mat) {
       float max = FLT_MIN;
       for (int r = 0; r < mat.rows; r++) {
           for (int c = 0; c < mat.cols; c++) {
               float val = mat.pData[r * mat.cols + c];
               max = (max > val ? max : val);
           }
       }
       return max;
   }
   ```

   if `Matrix::pData` is NULL or an invalid value, how to tell the calling function from the called one?

2. Where should a function be?

   ```C++
   // draw.h
   #ifndef __DRAW_H__	// 防止被多次调用重复定义
   #define __DRAW_H__
   bool drawLine(int, int, int, int);
   bool drawRectangle(int, int, int, int);
   #endif
   ```

   ```C++
   // draw.cpp
   #include <draw.h>
   
   bool drawRectangle(int x1, int x2, int y1, int y2) {
       //...
       drawLine(...);
       drawLine(...);
       drawline(...);
       drawLine(...);
       
       return true;
   }
   
   bool drawLine(int x1, int x2, int y1, int y2) {
       //...
       
       return true;
   }
   ```

   ```C++
   // mian.cpp
   #include <draw.h>
   
   int main() {
       //...
       drawRectangle(...);
       //...
       
       return 0;
   }
   ```

### Function Parameters

1. Parameters

   - The symbolic name for "data" that passes into a function

   Two ways to pass into a function:

   - Pass by value
   - Pass by reference

2. Pass by value: fundamental type(`int`, `double`, `float`, ...)

   - The parameter is a copy of the original variable

   ```C++
   int foo(int x) {
       // x is a copy
       x += 10;
       return x;
   }
   
   int main() {
       int num1 = 20;
       int num2 = foo(num1);	// num1 will not be changed
       
       return 0;
   }
   ```

3. Pass by value: pointer

   ```C++
   int foo(int* p) {
       (*p) += 10;
       return *p;
   }
   
   int main() {
       int num1 = 20;
       int* p = &num1;
       // num1 will be changed
       // It still is passing by value (the address)
       // A copy of the address
       int num2 = foo(p);
   	
       return 0;
   }
   ```

4. Pass by value: structure

   ```C++
   struct Matrix {
       int rows;
       int cols;
       float* pData;
   }
   
   float matrix_max(struct Matrix mat) {
       float max = FLT_MIN;
       for (int r = 0; r < mat.rows; r++) {
           for (int c = 0; c < mat.cols; c++) {
               float val = mat.pData[r * mat.cols + c];
               max = (max > val ? max : val);
           }
       }
       return max;
   }
   ```


### References

1. References in C++

   - References are in C++, not in C
   - A reference is an alias(别名) to an already-existing variable/object

   ```C++
   int num = 10;
   int& num_ref = num;
   
   num_ref = 10;
   cout << "num = " << num << endl;
   
   // "num = 10"
   ```

   - A reference to an object

   ```C++
   struct Matrix {
       int rows;
       int cols;
       float* pData;
   }
   
   Matrix matA = {3, 4};
   mat.pData = new float[matA.rows * matA.cols]{}
   
   Matrix& matA_ref = matA;
   ```

   - A reference must be initialized after its declaration
   - Reference VS Pointer: References are much safer

2. Function parameters with a huge structure

   - If the huge struct is passed as a function parameter

   ```C++
   struct PersonInfo {
       char firstname[256];
       char middlename[256];
       char lastname[256];
       char address[256];
       char nationalID[16];
       // and more
   };
   
   char* fullname(struct PersonInfo pi) {
       // ...
   }
   ```

   The data will be copied. Not a good choice

   - One solution is to use a pointer

   ```C++
   char* fullname(struct PersonInfo* ppi) {
       if (ppi == NULL) {
           cerr << "Invalid pointer" << endl;
           return NULL;
       }
       // ...
   }
   ```

3. References as a function parameters

   - No data copying in the reference version; Better efficiency
   - The modification to a reference will affect the original object

   ```C++
   char* fullname(struct PersonInfo& pi) {
       // ...
   }
   ```

   - To avoid the data is modified by mistakes

   ```C++
   char* fullname(const struct PersonInfo& pi) {
       // ...
   }
   ```

### Return statement

- Statement `return;` is only vaild if the function return type is `void`
- Just finish the execution of the function, no value returned

```C++
void print_gender(bool isMale) {
    if (isMale) {
        cout << "Male" << endl;
    } else {
        cout << "Female" << endl;
    }
    
    return;
}

void print_gender(bool isMale) {
    if (isMale)
        cout << "Male" << endl;
    else
        cout << "Female" << endl;
}
```

- The return type can be a fundamental type or a compound type

- Pass by value:

  - Fundamental types: the value of a constant/variable is copied
  - Pointers: the address is copied
  - Structures: the whole structure is copied

  ```C++
  float maxa = matrix_max(matA);
  
  Matrix* pMat = creat_matrix(4, 5);
  ```

1. If we have a lot to return 

   - Such as a matrix addition function (A + B -> C)
   - A suggested prototype:
     - To use references to avoid data copying
     - To use const parameters to avoid the input data is modified
     - To use non-const reference parameters to receive the output

   ```C++
   bool matrix_add(const Matrix& matA, const Matrix& matB, Matrix& matC) {
       // check the dimensions of the three matrices
       // re-create matC if needed
       // do: matC = matA + matB
       // return true if everything is right
   }
   ```

   **C 语言的返回值只能有一个**

2. Similar mechanism in OpenCV

   - Matrix add in OpenCV

   ```C++
   void cv::add(InputArray src1, InputArray src2, OutputArray dst, InputArray mask, int dtype) {
       CV_INSTRUMENT_REGION();
       
       arithm(src1, src2, mask, dtype, getAddTab(), false);
   }
   ```

### Inline function(内联函数)

1. Inline function 

   - Stack operations and jumps are needed for a function call
   - It is a heavy cost for some frequently called *tiny* functions

   ```C++
   float max_function(float a, float b) {
       if (a > b) {
           return a;
       } else {
           return b;
       }
   }
   
   int main() {
       int num1 = 20;
       int num2 = 30;
       int maxv = max_function(num1, num2);
       
       // ...
   }
   ```

   - The generated **instructions** by a compiler can be as follows to improve efficiency

   ```C++
   int main() {
       int num1 = 20;
       int num2 = 30;
       int maxv = {
           if (num1 > num2) {
               return num1;
           } else {
               return num2;
           }
       };
   }
   ```

   - `inline` suggests the compiler to perform that kind of optimizations
   - The compiler may not follow your suggestion if the function is too complex or contains some constrains
   - Some functions without `inline` may be optimized as an inline one

   ```C++
   inline float max_function(float a, float b) {
       if (a > b) {
           return a;
       } else {
           return b;
       }
   }
   ```

2. Why not use a macros?

   ```C++
   #define MAX_MACROS(a, b) (a) > (b) ? (a) : (b)
   ```

   - The source code will be replaced by a preprocessor 
   - Surely no cost of a function call
   - And `a, b` can be any types which can compare

   ```C++
   int main() {
       int num1 = 20;
       int num2 = 30;
       int maxv = max_function(num1++, num2++);
       cout << maxv << endl;	// 30
       // num1 = 21, num2 = 31
       
       maxv = MAX_MACROS(num1++, num2++);
       cout << maxv << endl;	// 32
       // num1 = 22, num2 = 33
   }
   ```

## WEEK07

---

### Default Arguments

1. Default arguments

   - A feature in C++ (not C)
   - To call a function without providing one or more trailing(拖尾的) arguments.

   ```C++
   float norm(float x, float y, float z);
   float norm(float x, float y, float z = 0);
   // 0 is a argument, z is a parameter
   float norm(float x, float y = 0, float z);
   // 分别设置了 y 和 z 的默认参数均为 0，默认参数的定义一定是从尾部开始定义的
   // 默认参数不可以重定义，只可以设置一次
   ```

### Function Overloading(函数重载)

- A feature in C++ (not C)

1. Why to overload?

   - C99

     ```C
     // <math.h>
     
     double		round	(double x);
     float		roundf	(float x);
     long double	roundl	(long double x);
     ```

   - C++11

     ```C++
     // <cmath>
     
     double		round	(double x);
     float		round	(float x);
     long double	round	(long double x);
     ```

2. Function overloading

   - Which function to choose? the compiler will perform name lookup
   - Argument-dependent lookup, also known as ADL
   - The return type will not be considered in name lookup
   - **Functions that differ only in their return type cannot be overload**

### Function Templates(模板)

1. Why function templates

   - The definitions of some overloaded functions may be similar

   ```C++
   int sum(int x, int y) {
       cout << "sum(int, int) is called" << endl;
       return x + y;
   }
   
   float sum(float x, float y) {
       cout << "sum(float, float) is called" << endl;
       return x + y;
   }
   
   double sum(double x, double y) {
       cout << "sum(double, double) is called" << endl;
       return x + y;
   }
   ```

2. Explicit instantiation(显式实例化)

   - A function template is not a type, or a function, or any other entity
   - No code is generated from a source file that contains only template definitions
   - The template arguments must be determined, then the compiler can generate an actual function

   ```C++
   template<typename T>
   T sum(T x, T y) {
       cout << "The input type is " << typeid(T).name() << endl;
       return x + y;
   }
   
   // instantiation
   template double sum<double>(double, double);
   template char sum<>(char, char);
   template int sum(int, int);
   ```

3. Implicit instantiation(隐式实例化)

   - Implicit instantiation occurs when a function template is not explicitly instantiated

   ```C++
   template<tempname T>
   T product(T x, T y){
       cout << "The input type is " << typeid(T).name() << endl;
       return x * y;
   }
   
   // Implicitly instantiates product<int>(int, int)
   cout << "product = " << product<int>(2.2f, 3.3f) << endl;
   // Implicitly instantiates product<float>(float, float)
   cout << "product = " << product(2.2f, 3.3f) << endl;
   ```

4. Function template specialization

   - We have a function template 

     ```C++
     template<typename T> T sum(T x, T y);
     ```

   - If the input type is `Point`

     ```C++
     struct Point {
         int x;
         int y;
     };
     ```

   - But no operator for `Point`

   - We need to give a special definition for this case

   ```C++
   template<typename T>
   T sum(T x, T y) {
       cout << "The input type is " << typeid(T).name() << endl;
       return x + y;
   }
   
   // Specialization for Point + Point operation
   template<>
   Point sum<Point>(Point pt1, Point pt2) {
       cout << "The input type is " << typeid(pt1).name() << endl;
       Point pt;
       pt.x = pt1.x + pt2.x;
       pt.y = pt1.y + pt2.y;
       
       return pt;
   }
   ```

### Function Pointers and References

1. Function pointers

   - `norm_ptr` is a pointer, a function pointer
   - The function should have two `float` parameters, and returns `float`

   ```C++
   float norm_l1(float x, float y);
   
   float norm_l2(float x, float y);
   
   float (*norm_ptr)(float x, float y);
   
   norm_ptr = norm_l1;	// Pointer norm_ptr is pointing to norm_l1
   
   norm_ptr = &norm_l2;	// Pointer norm_ptr is pointing to norm_l1
   
   float len1 = norm_ptr(-3.f, 4.f);	// function invoked
   
   float len2 = (*norm_ptr)(-3.f, 4.f);// function invoked
   ```

   - A function pointer can be an argument and pass to a function

     ```C++
     // <stdlib.h>
     
     void qsort(void* ptr, size_t count, size_t size, int (*comp)(const void*, const void*));
     ```

     - To sort some customized types

2. Function references

   ```C++
   float norm_l1(float x, float y);	// declaration
   float norm_l2(float x, float y);	// declaration
   float (&norm_ref)(float x, float y) = norm_l1;	// norm_ref is a function reference
   // A reference must be initialized after its declaration
   ```

### Recursive(递归) Function

1. A simple example

   ```C++
   int main() {
       div2(1024.);	// call the recursive function
       return 0;
   }
   
   void div2(double val) {
       cout << "Entering val = " << val << endl;
       
       if (val > 1.0) {
           div(val / 2);	// function calls itself
       } else {
           cout << "----------" << endl;
       }
       
       cout << "Leaving val = " << val << endl;
   }
   ```

2. Pros and Cons

   - Pros
     - Good at tree traversal
     - Less lines of source code
   - Cons
     - Consume more stack memory
     - May be slow
     - Difficult to implement and debug

## WEEK08

---

### C/C++ with ARM

1. Intel VS ARM
   - With the help of C/C++ compilers, C and C++ are platform independent
   - But we need to know some background information on different CPUs
   - Intel achieved a dominant position the personal computer market
2. ARM
   - ARM(previously an acronym for Advanced RISC Machine and originally Acron RISC Machine) is a family of reduced instruction set computing (RISC) architectures for computer processors
   - ARM is the most widely used instruction set architecture (ISA) and the ISA produced in the largest quantity

### Speedup Your Program

1. Some Tips on Optimization
   - Choose an appropriate algoithm
   - Clear and simple code for the compiler to optimize 
   - Optimize code for memory
   - Do not copy large memory
   - No printf()/cout in loops
   - Table lookup
2. SIMD: Single instruction, multiple data
   - Intel: MMX, SSE, SSE2, AVX, AVX2, AVX512
   - ARM: NEON
   - RISC-V(开源指令集): RVV(RISC-V Vector Extension)

### An Example with SIMD and OpenMP

1. Functions for dot product

   ```C++
   // matoperation.hpp
   #pragma once
   
   float dotproduct(const float* p1, const float* p2, size_t n);
   float dotproduct_unloop(const float* p1, const float* p2, size_t n);
   float dotproduct_avx2(const float* p1, const float* p2, size_t n);
   float dotproduct_avx2_omp(const float* p1, const float* p2, size_t n);
   float dotproduct_neon(const float* p1, const float* p2, size_t n);
   float dotproduct_neon_omp(const float* p1, const float* p2, size_t n);
   ```

   ```C++
   // matoperation.cpp
   
   #include <iostream>
   #include "matoperation.hpp"
   
   #ifdef WITH_AVX2
   #include <immintrin.h>
   #endif
   
   #ifdef WITH_NEON
   #include <arm_neon.h>
   #endif
   
   #ifdef _OPENMP
   #include <omp.h>
   #endif
   
   float dotproduct(const float* p1, const float* p2, size_t n) {
       float sum = 0.0f;
       for (size_t i = 0; i < n; i++) {
           sum += (p1[i] * p2[i]);
       }
       
       return sum;
   }
   
   float dotproduct_unloop(const float* p1, const float* p2, size_t n) {
       if (n % 8 != 0) {
           std::cerr << "The size n must be a multiple of 8." << std::endl;
           
           return 0.0f;
       }
       
       float sum = 0.0f;
       for (size_t i = 0; i < n; i += 8) {
           sum += (p1[i] * p2[i]);
           sum += (p1[i+1] * p2[i+1]);
           sum += (p1[i+2] * p2[i+2]);
           sum += (p1[i+3] * p2[i+3]);
           sum += (p1[i+4] * p2[i+4]);
           sum += (p1[i+5] * p2[i+5]);
           sum += (p1[i+6] * p2[i+6]);
           sum += (p1[i+7] * p2[i+7]);
       }
       
       return sum;
   }
   
   float dotproduct_avx2(const float* p1, const float* p2) {
       #ifdef WITH_AVX2
      	if (n % 8 != 0) {
           std::cerr << "The size n must be a multiple of 8." << std::endl;
               
           return 0.0f;
       }
       	
       float sum[8] = {0};
       __m256 a, b;
       __m256 c = _mm256_setzero_ps();
       
       for (size_t i = 0; i < n; i += 8) {
           a = _mm256_load_ps(p1 + i);
           b = _mm256_load_ps(p2 + i);
           c = _mm256_add_ps(c, _mm256_mul_ps(a, b));
       }
       _mm256_store_ps(sum, c);
       
       return (sum[0] + sum[1] + sum[2] + sum[3] + sum[4] + sum[5] + sum[6] + sum[7]);
       
       #else
       std::cerr << "AVX2 is not supported" << std::endl;
       return 0.0f;
       #endif
   }
   
   float dotproduct_avx2_omp(const float* p1, const float* p2) {
       #ifdef WITH_AVX2
      	if (n % 8 != 0) {
           std::cerr << "The size n must be a multiple of 8." << std::endl;
               
           return 0.0f;
       }
       	
       float sum[8] = {0};
       __m256 a, b;
       __m256 c = _mm256_setzero_ps();
       
       #pragma omp parallel for
       for (size_t i = 0; i < n; i += 8) {
           a = _mm256_load_ps(p1 + i);
           b = _mm256_load_ps(p2 + i);
           c = _mm256_add_ps(c, _mm256_mul_ps(a, b));
       }
       _mm256_store_ps(sum, c);
       
       return (sum[0] + sum[1] + sum[2] + sum[3] + sum[4] + sum[5] + sum[6] + sum[7]);
       
       #else
       std::cerr << "AVX2 is not supported" << std::endl;
       return 0.0f;
       #endif
   }
   
   float dotproduct_neon(const float* p1, const float* p2) {
       #ifdef WITH_NEON
      	if (n % 4 != 0) {
           std::cerr << "The size n must be a multiple of 4." << std::endl;
               
           return 0.0f;
       }
       	
       float sum[4] = {0};
       float32x4 a, b;
       float32x4 c = vdupq_n_f32(0);
       
       for (size_t i = 0; i < n; i += 4) {
           a = vld1q_f32(p1 + i);
           b = vld1q_f32(p2 + i);
           c = vaddq_f32(c, vmulq_f32(a, b));
       }
       vst1q_f32(sum, c);
       
       return (sum[0] + sum[1] + sum[2] + sum[3]);
       
       #else
       std::cerr << "NEON is not supported" << std::endl;
       return 0.0f;
       #endif
   }
   
   float dotproduct_neon_omp(const float* p1, const float* p2) {
       #ifdef WITH_NEON
      	if (n % 4 != 0) {
           std::cerr << "The size n must be a multiple of 4." << std::endl;
               
           return 0.0f;
       }
       	
       float sum[4] = {0};
       float32x4 a, b;
       float32x4 c = vdupq_n_f32(0);
       
       #pragma omp parallel for
       for (size_t i = 0; i < n; i += 4) {
           a = vld1q_f32(p1 + i);
           b = vld1q_f32(p2 + i);
           // 多线程写冲突导致结果错误
           c = vaddq_f32(c, vmulq_f32(a, b));
       }
       vst1q_f32(sum, c);
       
       return (sum[0] + sum[1] + sum[2] + sum[3]);
       
       #else
       std::cerr << "NEON is not supported" << std::endl;
       return 0.0f;
       #endif
   }
   ```

   ```C++
   // main.cpp
   
   #include <iostream>
   #include <cstdlib>
   #include <chrono>
   #include "matoperation.hpp"
   using namespace std;
   
   #define TIME_START start = std::chrono::steady_clock::now();
   #define TIME_END(NAME) end = std::chrono::steady_clock::now();\
   					duration = std::chrono::duration_cast<std::chrono::milliseconds> (end - start).count();\
   					cout << (NAME) << ": result = " << result\
   					<< ", duration = " << duration << "ms" << endl;
   
   int main(int argc, char** argv) {
       size_t nSize = 200000000;
       // float* p1 = new float[nSize]();	// the memory is not aligned
       // float* p2 = new float[nSize]();	// the memory is not aligned
       
       // 256bit aligned, C++17 standard
       float* p1 = static_cast<float*>(aligned_alloc(256, nSize * sizeof(float)));
       float* p2 = static_cast<float*>(aligned_alloc(256, nSize * sizeof(float)));
       float result = 0.0f;
       
       p1[2] = 2.3f;
       p2[2] = 3.0f;
       p1[nSize - 1] = 2.0f;
       p2[nSize - 1] = 1.1f;
       
       auto start = std::chrono::steady_clock::now();
       auto end = std::chrono::steady_clock::now();
       auto duration = 0L;
       
       // 热身
       result = dotproduct(p1, p2, nSize);
       result = dotproduct(p1, p2, nSize);
       
       TIME_START
       result = dotproduct(p1, p2, nSize);
       TIME_END("normal")
           
       TIME_START
       result = dotproduct_unloop(p1, p2, nSize);
       TIME_END("unloop")
           
       TIME_START
       result = dotproduct_neon(p1, p2, nSize);
       TIME_END("SIMD")
       
       TIME_START
       result = dotproduct_neon_omp(p1, p2, nSize);
       TIME_END("SIMD + OpenMP")
           
       delete []p1;
       delete []p2;
       
       return 0;
   }
   ```

### Avoid Memory Copy

1. `cv::Mat class`

   ```C++
   class CV_EXPORTS Mat {
       public:
       // some variables
       int rows, cols;
       int* refcount;	// 是否释放内存
       // pointer to data
       uchar* data;
       // size_t step.p
       MatStep step;	// 每一行元素有多少个字节
   }
   ```

2. step in `cv::Mat`

   - Can be 3, 4, 8, and any other values >= 3

   - Memorry alignment for SIMD

   - ROI: Region of interest

     - Mat A
       - rows = 100
       - cols = 100
       - step = 100
     - Mat B
       - rows = 30
       - cols = 28
       - step = 100

     Mat B 为 Mat A 矩阵中的 ROI，为了避免数据的复制，引入 step 用于计算 ROI 在内存中的位置

## WEEK09

### Classes and Objects

1. Structures

   - A `struct` in C is a type consisting of a sequence of data members
   - Some functions/statements are needed to operate the data members of an object of a `struct` type

   ```C
   struct Student {
       char name[4];
       int born;
       bool male;
   };
   
   struct Student stu;
   strcpy(stu.name, "Yu");
   stu.born = 2000;
   stu.male = true;
   ```

2. Classes

   - You should be very careful to manipulated the data members in a `struct` object
   - We can put some member functions in `class`

   ```C++
   class Student {
       public:
       char name[4];
       int born;
       bool male;
       
       void setName(const char* s) {
           strncpy(name, s, sizeof(name));
       }
       
       void setBorn(int b) {
           //...
       }
   };
   
   Student stu;
   stu.setName("Yu");
   ```

3. Access Specifiers

   - You can protect data members by access specifier `private`
   - Then data member can only be accessed by well designed member functions
   - 默认为 `private`

   ```C++
   class Student {
       private:
       char name[4];
       int born;
       bool male;
       
       public:
       void setName(const char* s) {
           strncpy(name, s, sizeof(name));
       }
       
       void setBorn(int b) {
           // ...
       }
   };
   ```

4. Member Functions

   - A member function can be defined inside or outside class

   ```C++
   class Student {
       private:
       char name[4];
       int born;
       bool male;
       
       public:
       void setName(const char* s) {
           strncpy(name, s, sizeof(name));
       }
       
       void setBorn(int b) {
           born = b;
       }
       
       void setGender(bool isMale);
       
       void printInfo();
   };
   
   inline void Student::setGender(bool isMale) {
       male = isMale;
   }
   
   void Student::printInfo() {
       cout << "Name: " << name << endl;
       cout << "Born in: " << born << endl;
       cout << "Gender: " << (male ? "Male" : "Female") << endl; 
   }
   ```

   对类的定义一般放在 `.hpp` 头文件中，而将所有的函数放在 `.cpp` 中。

   {{<admonition tip>}}

   `include <XXX>` 与 `include "XXX"` 的区别：

   `include <XXX>` 使得编译器从编译器指定的头文件目录去寻找头文件

   `include "XXX"` 使得编译器不止从编译器指定的头文件目录中去寻找头文件，而且在 `.cpp` 的当前目录下寻找头文件

   {{</admonition>}}

### Constructors(构造函数) and Destructors(析构函数)

1. Constructors

   - Different from `struct` in C, a constructor will  be invoked(调用) when creating an object of a `class`
     - `struct` in C: allocate memory
     - `class` in C++: allcoate memory & invoke a constructor
     
   - But ... No constructor is defined explicitly in previous examples
     - The compiler will generate one with empty body
     
   - Constructor
     - The same name with the class
     
     - Have no return value
     
     - 构造函数可以重载
     
       ```C++
       class Student {
           private:
           // ...
           public:
           Student() {
               name[0] = 0;
               born = 0;
               male = false;
           }
           
           Student(const char* initName, int initBorn, bool isMale) {
               setName(initName);
               born = initBorn;
               male = isMale;
           }
       };
       ```
     
     - The members can also be initialized as follows
     
       ```C++
       Student(const char* initName): born(0), male(true) {
           setName(initName);
       }
       ```
     
     - 构造函数的调用
     
       ```C++
       Student yu;
       
       Student li("Li");
       
       Student xue = Student("Xue", 1962, true);
       
       Student* zhou = new Student("Zhou", 1991, false);
       ```

2. Desturctors

   - The destructor will be invoked when the object is destroyed
   - Be formed from the class name preceded by a tilde(~)
   - Have no return value, no parameters
   - 析构函数不可以重载
   - 当一个对象退出其作用域时会被自动销毁

   ```C++
   class Student {
       private:
       // ...
       public:
       Student() {
           name = new char[1024]{0};
           born = 0;
           male = false;
       }
       
       ~Student() {
           delete [] name;
       }
   };
   
   Student* zhou = new Student("Zhou", 1991, false);
   
   delete zhou;	// 手动销毁
   ```

   - `delete` 和 `delete[]` 之间的区别：

     ```C++
     Student* class1 = new Student[3] {
         {"Tom", 2000, true},
         {"Bob", 2001, true},
         {"Amy", 2002, false},
     };
     
     delete class1;	// 只会调用第一个成员的析构函数
     delete[] class1;	// 会调用所有成员的析构函数
     ```

### `this` Pointer

1. Why is `this` needed?

   - How does a member function know which `name`?

   ```C++
   void setName(const char* s) {
       strncpy(name, s, 1024);
   }
   
   Student yu = Student{"Yu", 2000, true};
   Student amy = Student{"Amy", 2000, true};
   yu.setName("yu");
   amy.setName("Amy");
   ```

2. `this` Pointer

   - All methods in a function have a `this` pointer
   - It is set to the address of the object that invokes the method

   ```C++
   void setBorn(int b) {
       born = b;
   }
   
   void setBorn(int b) {
       this -> born = b;
   }
   
   void setBorn(int born) {
       this -> born = born;
   }
   ```

### `const` and `static` Members

1. `const` variables

   - Statements for constants

     ```C++
     #define VALUE 100
     
     const int value = 100;
     
     const int* p_int;	// 指针指向的内容不可以透过指针修改
     int const *p_int;
     
     int* const p_int;	// 指针指向的地址不可以被修改
     
     void func(const int*);
     void func(const int&);
     ```

2. `const` members

   - `const` member variables behavior similar with normal const varibales
   - `const` member functions promise not to modify member variables

   ```C++
   class Student {
       private:
       const int BMI = 24;
       // ...
       
       public:
       Student() {
           BMI = 25;	// can't be modified
       }
       
       int getBorn() const {
           born++;	// can't be modified
           return born;
       }
   };
   ```

3. `static` members

   - `static` members are not bound to class instance

   ```C++
   class Student {
       private:
       static size_t student_total;	// declarration only;
       inline static size_t student_total = 0;	// C++17, definition outside isn't needed
       
       public:
       Student() {
           student_total++;
       }
       
       ~Student() {
           student_total--;
       }
       
       static size_t getTotal() {
           return student_total;
       }
       
       // definition it here
       size_t Student::student_total = 0;
   }
   ```

## WEEK10

### Opeators in OpenCV

1. operators for `cv::Mat`

   ```C++
   #include <iostream>
   #include <opencv2/opencv.hpp>
   
   using namespace std;
   
   int main() {
       float a[6] = {1.0f, 1.0f, 1.0f, 2.0f, 2.0f, 2.0f};
       float b[6] = {1.0f, 1.0f, 1.0f, 2.0f, 2.0f, 2.0f};
       
       cv::Mat A(2, 3, CV_32FC1, a);
       cv::Mat B(3, 2, CV_32FC1, b);
       
       cv::Mat C = A * B;
       
       cout << "Matrix C = " << endl << C << endl;
       
       return 0;
   }
   ```

2. operator overloading

   - Customizes the C++ operators for operands(操作数) of user-defined types

   - Overloading operators are functions with special function names

     ```C++
     std::string s("Hello ");
     // `s +=` 与 `s.operator+=` 完全等价
     s += "C";
     s.operator+=(" and CPP!");
     ```

### Operator Overloading

1. operator overloading

   - implementation  of `operator+()` and `operator+=()`

   ```C++
   class MyTime {
       int hours;
       int minutes;
       
       public:
       MyTime(): hours(0), minutes(0) {}
       MyTime(int h, int m): hours(h), minutes(m) {}
       
       MyTime operator+(const MyTime & t) const {
           MyTime sum;
           sum.minutes = this -> minutes + t.minutes;
           sum.hours = this -> hours + t.hours;
           sum.hours += sum.minutes / 60;
           sum.minutes %= 60;
           
           return sum;
       }
       
       MyTime & operator+=(const MyTime & t) {
           this -> minutes += t.minutes;
           this -> hours += t.hours;
           
           this -> hours += this -> minutes / 60;
           this -> minutes %= 60;
           
           return *this;
       }
       
       std::string getTime() const {
           return std::to_string(this -> hours) + "hours and " + std::to_string(this -> minutes) + " minutes.";
       }
   };
   ```

   - If one operand is not MyTime, and is an `int`

   ```C++
   MyTime t1(2, 40);
   MyTime t2 = t1 + 20;
   ```

   - The function can be

   ```C++
   MyTime operator+(int m) const {
       MyTime sum;
       sum.minutes = this -> minutes + m;
       sum.hours = this -> hours;
       sum.hours += sum.minutes / 60;
       sum.minutes %= 60;
       
       return sum;
   }
   ```

   - We can even support the following operation to be more user friendly

   ```C++
   MyTime t1(2, 40);
   MyTime t2 = t1 + "one hours";
   
   MyTime operator+(const std::string str) const {
       MyTime sum = *this;
       if (str == "one hours") {
           sum.hours = this -> hours + 1;
       } else {
           std::cerr << "only \"one hours\" is supported." << std::endl;
       }
       
       return sum;
   }
   ```

   - Overloaded operators is more user-friendly than functions
   
   ```C++
   t1 + 20;	// operator
   t1.operator+(20);	// equivalent function invoking
   ```

### `friend` Function(友元函数)

1. `friend` function

   - If we want that operator + can support (`int` + `MyTime`)

     ```C++
     MyTime t1(2, 40);
     20 + t1;
     ```

   - Friend functions

     - Declare in a class body
     - Granted class access to members (including private members)
     - But **not** members

   - Again, friend functions are not members! They just declareed in the class body

     ```C++
     class MyTime {
         // ...
         public:
         friend MyTime operator+(int m, const MyTime & t) {
             return t + m;
         }
     };
     ```

   - A friend function is defined out of the class

   - No `MyTime::` before its function name

     ```C++
     class MyTime {
         // ..
         public:
         friend MyTime operator+(int m, const MyTime & t);
     }
     
     MyTime operator+(int m, const MyTime & t) {
         return t + m;
     }
     ```

   - Operator `<<` can also be overloaded

   - But in (`cout << t1;`), the frist operand is `std::ostream`, not MyTime.

   - To modify the definition of `std::ostream`? No

   - Use a friend function

     ```C++
     friend std::ostream & operator<<(std::ostream & os, const MyTime & t) {
         std::string str = std::to_string(t.hours) + " hours and " + std::to_string(t.minutes) + " minutes.";
         os << str;
         
         return os;
     }
     
     friend std::istream & operator>>(std::istream & is, MyTime & t) {
         is >> t.hours >> t.minutes;
         t.hours += t.minutes;
         t.minutes %= 60;
         
         return is;
     }
     ```

### User-defined Type Conversion

1. opertor `type()`

   - Overloading type conversion: convert the current type to another

     ```C++
     // implicit conversion
     operator int() const {
         return this -> hours * 60 + this -> minutes;
     }
     
     // explicit conversion
     // 意味着类型转换必须显式的进行
     explicit operator float() const {
         return float(this -> hours * 60 + this -> minutes);
     }
     ```

     ```C++
     MyTime t1(1, 20);
     
     int minutes = t1;	// implicit conversion
     float f = float(t1);	// explicit conversion
     ```

2. converting constructor

   - Convert anther type to the current

     ```C++
     MyTime(int m): hours(0), minutes(m) {
         this -> hours += this -> minutes / 60;
         this -> minutes %= 60;
     }
     ```

     ```C++
     MyTime t2 = 70;
     ```

3. Assignment operator overloading

   - Convert anther type to the current

     ```C++
     MyTime & operator=(int m) {
         this -> hours = 0;
         this -> minutes = m;
         this -> hours = this -> minutes / 60;
         this -> minutes %= 60;
         
         return *this;
     }
     ```

     ```C++
     MyTime t3;
     t3 = 80;
     ```

4. Be careful

   - What is the difference in creating object `t2` and `t3`?

     ```C++
     MyTime t2 = 70;	// 调用构造函数
     
     MyTime t3;	
     t3 = 80;	// 赋值
     ```

### Increment and decrement operators(自增自减运算符)

1. Increment

   - Two operators: prefix increment & postfix increment

     ```C++
     // prefix increment
     MyTime& operator++() {
         this -> minutes++;
         this -> hours += this -> minutes / 60;
         this -> minutes = this -> minutes % 60;
         
         return *this;
     }
     
     // postfix increment
     MyTime operator++(int) {
         MyTime old = *this;	// keep the old value
         operator++();	// prefix increment
         
         return old;
     }
     ```

2. Decrement

   - 与自增同理

## WEEK11

---

### Some Default Operations

1. Default Constructors

   - Default constructor: a constructor which can be called without argument

   - If you define no constroctors, the compiler automatically provide one

     ```C++
     MyTime::MyTime(){}
     ```

   - If you define constructors, the compiler will not generate a default one

     ```C++
     class MyTime {
         public:
         MyTime(int n) {
             // ...
         }
     };
     
     MyTime mt;	// no appropriate constructor
     ```

   - To avoid ambigous

     ```C++
     class MyTime {
         public:
         MyTime() {
             // ...
         }
         
         MyTime(int n = 0) {
             // ...
         }
     };
     
     MyTime mt;	// which constructor?
     ```

2. Implicitly-defined Destructor

   - If no destructor is defined, the compiler will generate an empty one 

     ```C++
     MyTime::~MyTime(){}
     ```

   - Memory allocated in constructors is normally released in a destructor

3. Default Copy Constructors

   - A copy constructor. Only one parameter, or the rest have default  values

     ```C++
     MyTime::MyTime(MyTime & t) {
         // ...
     }
     ```

     ```C++
     MyTime t1(1, 59);
     MyTime t2(t1);	// copy constructor
     MyTime t3 = t1;	// copy constructor
     ```

   - Default copy constructor

     - If no user-defined copy constructors, the compiler will generate one
     - Copy all non-static data member

4. Default Copy Assignment

   - Assignment operators: `=`, `+=`, `-=`, ...

   - Copy assignment operator

     ```C++
     MyTime & MyTime::operator=(MyTime & ){
         // ...
     }
     ```

     ```C++
     MyTime t1(1, 59);
     MyTime t2 = t1;	// copy constructor
     t2 = t1;	// copy assignment
     ```

   - Default copy assignment operator

     - If no user-defined copy assignment constructors, the compiler will generate one
     - Copy all non-static data members

### An example with Dynamic Memory

1. A Simple String Class

   ```C++
   class MyString {
       int buf_len;
       char* characters;
       
       public:
       MyString(int buf_len = 64, const char* data = NULL) {
           this -> buf_len = 0;
           this -> characters = NULL;
           
           create(buf_len, data);
       }
       
       ~MyString() {
           delete []this -> characters;
       }
   }
   ```

   ```C++
   MyString str1(10, "Shenzhen");
   
   MyString str2 = str1;	
   // 这会导致 str2.characters 与 str1.characters 指针指向相同的位置
   // 从而在释放内存时出现重复释放
   // 不应该使用默认复制构造函数
   ```

### Solution 1: Hard Copy

1. Copy Constructor

   - Provide a user-defined copy constructor

     ```C++
     MyString::MyString(const MyString & ms) {
         this -> buf_len = 0;
         this -> characters = NULL;
         create(ms.buf_len, ms.characters);
     }
     ```

   - `create()` relase the current memory and allocate a new one

   - `this -> characters` will not point to `ms.characters`

   - It's a hard copy

2. Copy Assignment

   - Provide a user-defined copy assignment

     ```C++
     MyString & operator=(const MyString & ms) {
         create(ms.buf_len, ms.characters);
         
         return *this;
     }
     ```

### Solution 2: Soft Copy

1. Problem of Hard Copy

   - Frequently allocate and free memory
   - Time consuming when the memory is big
   - If several objects share the same memory, who should release it?

2. CvMat struct

   ```C
   typedef struct CvMat {
       int type;
       int step;
       
       /* for internal use only */
       int* refcount;
       int hdr_refcount;
       
       union {
           uchar* ptr;
           short* s;
           int* i;
           float* fl;
           double* db;
       } data;
   }
   ```

### Smart Pointers

1. `std::shared_ptr`

   - Smart pointers are used to make sure that an object can be delete when it is no longer used

   - Several shared pointers can share/point to the same object

   - The object is destroyed when no shared_ptr points to it

     ```C++
     std::shared_ptr<MyTime> mt1(new MyTime(10));
     std::shared_ptr<MyTime> mt2 = mt1;
     
     std::shared_ptr<MyTime> mt1 = std::make_shared<MyTime>(1, 70);
     ```

2. `std::unique_ptr`

   - Different from `std::shared_ptr`, a `std::unique_ptr` will point to an object, and not allow others to point to

   - But an object pointed by a `std::unique_ptr` can be moved to another pointer

     ```C++
     std::unique_ptr<MyTime> mt1(new MyTime(10));
     std::unique_ptr<MyTime> mt2 = std::make_unique<MyTime>(80);
     
     std::unique_ptr<MyTime> mt3 = std::move(mt1);
     ```

3. How to Understand Smart Pointers

   - Let's look at their definitions

     ```C++
     template<class T> class shared_ptr;
     
     template<
     	class T,
     	class Deleter = std::default_delete<T>
     	> class unique_ptr;
     ```

   - mt1 and mt2 are two objects of type shared_ptr<>

     - you can do a lot in the constructors and the destructor

       ```C++
       std::shared_ptr<MyTime> mt1(new MyTime(10));
       std::shared_ptr<MyTime> mt2 = mt1;
       ```

## WEEK12

---

### Improve Your Source Code

1. Suggestions to Your Project 3 (Mat Operation)
   - Use `size_t` for mat.cols and mat.rows
   - Use memcpy() to copy data. Element assignment has a lower efficiency
   - Use 1D(`float*`)  nor 2D array (`float**`) for matrix data
   - Redundant computation in loops
   - Do parameter checking in functions: null pointers, dimension matching in matrix operations, etc.
   - Do not bind the create matrix function with file I/O
   - File name
   - Good implementation VS good homework

### Derived Classes(类的继承)

1. Inheritance

   - Inherit members (attributes and functions) from one class

     - Base class (parent)
     - Derived class (child)

     ```C++
     class Base {
         public:
         int a;
         int b;
     };
     
     class Derived: public Base {
         public:
         int c;
     };
     ```

   - C++ supports multiple inheritance and multilevel inheritance

     ```C++
     class Derived: public Base1, public Base2 {
         // ...
     };
     ```

2. Constructors

   - To instantiate a derived class object

     - Allocate memory
     - Derived constructor is invoked
       - Base object is constructed by a base constructor
       - Member initializer list initializes members
       - To execute the body of the derived constrructor

     ```C++
     class Derived: public Base {
         public:
         int c,
         Derived(int c): Base(c - 2, c - 1), c(c) {
             // ...
         }
     };
     ```

3. Destructors

   - The destructor of the derived class is invoked first
   - Then the destructor of the base class

### Access Control

1. Member Access

   - Public members: Accessible anywhere

   - Private members: Only accessible to the members and friends of that class

     ```C++
     class Person {
         private:
         int n;
         public:
         // this -> n is accessible
         Person(): n(10) {}
         // other.n is accessible
         Person(const Person& other): n(other.n) {}
         // this -> n is accessible
         void set(int n) {this -> n = n;}
         // this -> n and other.n are accessible
         void set(const Person& other) {this -> n = other.n;}
     };
     ```

   - Protected members

     - Accessible to the members and friends of that class
     - Accessible to the members and friends of the derived class

     ```C++
     class Base {
         protected:
         int n;
         private:
         void foo1(Base& b) {
             n++;
             b.n++;
         }
     };
     
     class Derived: public Base {
         void foo2(Base& b, Derived& d) {
             n++;
             this -> n++;
             // b.n++;	// error
             d.n++;
         }
     };
     ```

2. Public Inheritance

   - Public members of the base class
     - Still public in the derived class
     - Accessible anywhere
   - Protected members of the base class
     - Still be protected in the derived class
     - Accessible in the derived class only
   - Private members of the base class
     - Not accessible in the derived class

3. Protect Inheritance

   - Public members and protected members of the base class
     - Be protected in the derived class
     - Accessible in the derived class only
   - Private members of the base class
     - Not accessible in the derived class

4. Private Inheritance

   - Public members and protected members of the base class
     - Be private in the derivate class
     - Accessible in the derivate class only
   - Private members of the base class
     - Not accessible in the derived class

### Virtual Functions(虚函数)

1. Virtual Functions

   - What will be output?

     ```C++
     class Person {
         public:
         virtual void print() {
             cout << "Name: " << name << endl;
         }
     };
     
     class Student: public Person {
         public:
         void print() {
             cout << "Name: " << name;
             cout << ". ID: " << id << endl;
         }
     };
     
     Person* p = new Student();
     p -> print();	// call Person::print()?
     ```

   - If we define `print()` function as a virtual function, the output will be different

   - `Static` binding: the compiler decides which function to call

   - `Dynamic` binding: the called function is decided at runtime

   - Keyword virtual makes the function virtual for the base and all derived classes

2. Virtual Destructors

   - If a virtual destructor is not virtual, only the destructor of the base class is executed in the fellow example

     ```C++
     Person* p = new Student("xue", "2020");
     p -> print;
     delete p;	// if its destructor is not virtual
     ```

### Inheritance and Dynamic Memory Allocation

1. Question

   - If a base class uses dynamic memory allocation, and redefines a copy constructor and assignment operator

     - Case 1: If no dynamic memory allocation in the derived class, no special operations are needed

     - Case 2: If dynamic memory is allocated in the derived class, you should redefine a copy constructor and an assignment operator 

       ```C++
       class MyMap: public MyString {
           char* keyname;
           public:
           MyMap(const char* key, const char* value) {
               // ...
           }
           
           MyMap(const MyMap& mm): MyString(mm.buf_len. mm.characters) {
               // allocate memory forr keyname
               // and hard copy from mm to *this
           }
           
           MyMap& operator=(const MyMap& mm) {
               MyString::operator=(mm);
               // allocate memory for keyname
               // and hard copy from mm to *this
               return *this;
           }
       };
       ```

## WEEK13

---

### Class Templates

1. Review: Function Templates

   - A function template is not a type, or a function, or any other entity

   - No code is generated from a source file that contains only template definitions

   - The template arguments must be determined, then the compiler can generate an actual function

   - "function templates" VS "template functions"

     ```C++
     template<typename T>
     T sum(T x, T y) {
         cout << "The input type is " << typeid(T).name() << endl;
         return x + y;
     }
     
     template double sun<double>(double, double);
     template char sum<>(char, char);
     template int sum(int, int);
     ```

   - Implicit instantiation occurs when a function template is not explicitly instantiated

     ```C++
     // implicit instantiates product<int>(int, int)
     cout << "product = " << product<int>(2.2f, 3.0f) << endl;
     // implicit instantiates product(2.2f, 3.0f)
     cout << "product = " << product(2.2f, 3.0f) << endl;
     ```

2. Different Classes for Different Type Matrices

   - Matrix with `int` elements, Matrix with `flaot` elements

     ```C++
     class IntMat {
         size_t rows;
         size_t cols;
         int* data;
         public:
         IntMat(size_t, rows, size_t, cols): rows(rows), cols(cols) {
             data = new int[rows * cols * sizeof(int)]{};
         }
         
         ~IntMat() {
             delete []data;
         }
         
         IntMat(const IntMat&) = delete;
         IntMat& operator=(const IntMat&) = delete;
         int getElement(size_t r, size_t c);
         bool setElement(size_t r, size_t c, int value);
     };
     
     class FloatMat {
         size_t rows;
         size_t cols;
         float* data;
         public:
         FloatMat(size_t rows, size_t cols): rows(rows) cols(cols) {
             data = new float[rows * cols * sizeof(float)]{};
         }
         
         ~FloatMat() {
             delete []data;
         }
         
         // 禁止编译器提供默认的构造函数
         FloatMat(const FloatMat&) = delete;
         FloatMat& operator=(const FloatMat&) = delete;
         float getElement(size_t r, size_t c);
         bool setElement(size_t r, size_t c, float value);
     };
     ```

3. Class Templates

   - A class template defines a family of classes

     ```C++
     template<typename T>
     class Mat {
         size_t rows;
         size_t cols;
         public:
         Mat(size_t rows, size_t cols): rows(rows), cols(cols) {
             data = new T[rows * cols * sizeof(T)]{};
         }
         
         ~Mat() {
             delete []data;
         }
         
         T getElement(size_t r, size_t c);
         bool setElement(size_t r, size_t c, T value);
     };
     
     template <typename T>
     T Mat<T>::getElement(size_t r, size_t c) {
         if (r >= this -> rows || c >= this -> cols) {
             cerr << "getElement(): Indices are out of range" << endl;
             
             return 0;
         }
         return data[this -> cols * r + c];
     }
     
     template <typename T>
     bool Mat<T>::setElement(size_t r, size_t c, T value) {
         if (r >= this -> rows || c >= this -> cols) {
             cerr << "setElement(): Indices are out of range" << endl;
             
             return false;
         }
        	data[this -> cols * r + c] = value;
         
         return true
     }
     
     // Explicitly instantiate
     template class Mat<int>;
     Mat<int> imat(3, 4);
     // template Mat<float> will be instantiate implicitly
     Mat<float> fmat(2, 3);
     ```


### Template Non-Type Parameters

1. Non-Type Parameters

   - To declare a template

     ```C++
     template<parameter-list> declaration
     ```

   - The parameters can be

     - type template parameters

     - template template parameters

     - non-type template parameters

       - integral types
       - floating-point types
       - pointer types
       - value reference types

       ```C++
       vector<int> vec1;
       vector<int, 16> vec2;
       ```

   - If we want to create a static matrix (no dynamic memory allocation inside)

     ```C++
     template<typename T, size_t rows, size_t cols>
     class Mat{
         T data[rows][cols];
         public:
         Mat(){}
         // the default copy constructor will copy element of a static array member
         // so we do not 'delete' the copy constructor
         // the same with the assignment operator
         T getElement(size_t r, size_t c);
        	bool setElement(size_t r, size_t c, T value);
     };
     
     template<typename T, size_t rows, size_t cols>
     T Mat<T, rows, cols>::getElement(size_t r, size_t c) {
         if (r >= rows || c >= cols) {
             cerr << "getElement(): indices are out of range" << endl;
             return 0;
         }
         return data[r][c];
     }
     
     template<typename T, size_t rows, size_t cols>
     T Mat<T, rows, cols>::setElement(size_t r, size_t c, T value) {
         if (r >= rows || c >= cols) {
             cerr << "setElement(): Indices are out of range" << endl;
             return false;
         }
         data[r][c] = value;
         return true;
     }
     ```

     ```C++
     Mat<int> vec1(3, 3);	// 动态申请
     Mat<int, 3, 3> vec2;	// 编译时确定
     ```

### Class Template Specialization

1. Class template specialization

   - The class template can be for most types

   - But we want to save memory for type `bool`

     ```C++
     template<typename T>
     class MyVector {
         size_t length;
         T* data;
         public:
         MyVector(size_t length): length(length) {
             data = new T[length * sizeof(T)]{};
         }
         
         ~MyVector() {
             delete []data;
         }
         
         MyVector(const MyVector&) = delete;
         MyVector& operator=(const MyVector&) = delete;
         T getElement(size_t index);
         bool setElement(size_t index, T value);
     };
     ```

   - Specialize `MyVector` for `bool`

     ```C++
     template<>
     class MyVector<bool> {
         size_t length;
         unsigned char* data;
         public:
         MyVector(size_t length): length(length) {
             int num_bytes = (length - 1) / 8 + 1;
             data = new unsigned char[num_bytes]{};
         }
         
         ~MyVector() {
             delete []data;
         }
         
         MyVector(const MyVector&) = delete;
         MyVector& operator=(const MyVector&) = delete;
         bool getElement(size_t index);
         bool setElement(size_t index, bool value);
     };
     
     bool MyVector<bool>::getElement(size_t index) {
         if (index >= this -> length) {
             cerr << "getElement(): Indices are out of range" << endl;
             return 0;
         }
         size_t byte_id = index / 8;
         size_t bit_id = index % 8;
         unsigned char mask = (1 << bit_id);
         return bool(data[byte_id] & mask);
     }
     
     bool MyVector<bool>::setElement(size_t index, bool value) {
         if (index >= this -> length) {
             cerr << "setElement(): Indices are out of range" << endl;
             return false;
         }
         
         size_t byte_id = index / 8;
         size_t bit_id = index % 8;
         unsigned char mask = (1 << bit_id);
         
         if (value) {
             data[byte_id] |= mask;
         } else {
             data[byte_id] &= ~mask;
         }
         
         return true;
     }
     ```

### `std` Class

1. `std::basic_string`

   - Store and manipulate sequences of char-like objects

     | Type           | Definition                   |
     | -------------- | ---------------------------- |
     | `std::string`  | `std::basic_string<char>`    |
     | `std::wstring` | `std::basic_string<wchar_t>` |

2. `std::array`

   - A container that encapsulates **fixed** size arrays

     ```C++
     template <
     	class T,
     	std::size_t N
     > strcut array;
     ```

     ```C++
     std::array<int, 3> a = {1, 2, 3};
     ```

     *Keyword: `typename`/`class`, `class`/`strcut`

3. Some other template

   ```C++
   template <
   	class T,
   	class Allocator = std::allocator<T>
   > class vector;
   
   template <
   	class T,
   	class Allocator = std::allocator<T>
   > class list;
   
   template <
   	class Key,
   	class Compare = std::less<Key>,
   	class Allocator = std::allocator<Key>
   > class set;
   
   template <
   	class Key,
   	class T,
   	class Compare = std::less<Key>,
   	class Allocator = std::allocator<std::pair<const Key, T>>
   > class map;
   
   template <
   	class T,
   	class Container = sed::deque<T>
   > class stack;
   ```

## WEEK14

---

### Standard Output Stream and Standard Error Stream

1. `stdin`, `stdout`, `stderr`

   - In C, three text streams are predefined, and their types is (`FILE *`)
   - `stdin`: standard input stream
   - `stdout`: standard output stream
   - `stderr`: standard error stream, for diagnostic output

2. Output Stream and Error Stream

   - Send contents into streams in C and C++

     ```C
     fprintf(stdout, "Info: ...\n", ...);
     printf("Info: ...\n", ...);
     
     fprintf(stderr, "Error: ...\n", ...);
     ```

     ```C++
     std::cout << "Info: ..." << std::endl;
     std::cerr << "Error: ..." << std::endl;
     ```

3. Redirection

   - The output of a program is in a pipeline
   - The output can be redirected. You can redirect the output into a file for debugging especially when the program run a very long time

   ```C
   void div2(int n) {
       if (n % 2 != 0) {
           fprintf(stderr, "Error: The input must be an even number. Here it's %d\n", n);
       } else {
           int result = n / 2;
           fprintf(stdout, "Info: The result is %d\n", result);
       }
       return;
   }
   
   int main() {
       for (int n = -5; n <= 5; n++) {
           div2(n);
       }
       
       return 0;
   }
   ```

   - `less`: 文本文件查看器

   ```sh
   ./a.out | less # 只有标准输出而没有错误输出，这是因为两种输出流不相同
   ./a.out > output.log	# 将输出重定向到文本文件中
   ./a.out 1> output.log	# 1 表示标准输出流，与上一行相同
   ./a.out >> output.log	# 两个箭头表示将内容附在原有内容之后，一个箭头表示销毁原有内容后重新生成
   ./a.out > /dev/null		# 直接销毁输出
   ./a.out 2> error.log	# 2 表示标准错误流，将标准错误流的输出重定向到文本文件中
   ./a.out > output.log 2> error.log	# 可以同时使用
   ./a.out &> all.out		# 表示全部重定向到文本文件
   ./a.out > all.out 2>&1	# 先将标准输出流重定向到文本文件，再将标准错误流重定向到标准输出流中
   ```

### `assert`

1. `assert`

   - `assert` is a function-like macro in `<assert.h>` and `<cassert>`

     ```C++
     #ifdef NDEBUG
     #define assert(conditon) ((void)0)
     #else
     #define assert(condition)	/* implementation defined */
     #endif
     ```

   - Do nothing if the condition is true

   - Output diagnostic information and call abort() if the condition is false

   - if `NDEBUG` is defined, do nothing whatever the condition is

   - assert can be used only for debugging, be removed by a macro `NDEBUG` before releasing

   ```C++
   #include <iostream>
   // #define NDEBUG
   #include <cassert>
   
   int main(int argc, char** argv) {
       assert(argc == 2);
       std::cout << "This is an assert example" << std::endl;
       return 0;
   }
   ```

   - 要点：
     1. `#define NDEBUG` 应该在 `include <cassert>` 之前
     2. 添加 `#define NDEBUG` 后，相当于注释了 `assert` 所在行
     3. 同样可以在编译时添加宏，使用 `-D` 参数，例如 `g++ assert.cpp -DNDEBUG`
   - Many applications define their own assert macros
   - `CV_Assert` in OpenCV checks a condition at runtime and throws exception if it fails

### Exceptions

1. Error Handling

   - Solution 1: Kill the program when error occurs

     ```C++
     float ratio(float a, float b) {
         if (fabs(a + b) < FLT_EPSILON) {
             std::cerr << "Error ..." << std::endl;
             std::abort();
         }
         
         return (a - b) / (a + b);
     }
     ```

   - Solution 2: Tell the caller by the return value when error occurs

   - We have to use the 3rd parameter to send the result

     ```C++
     bool ratio(float a, float b, float& c) {
         if (fabs(a + b) < FLT_EPSILON) {
             std::cerr << "Error ..." << std::endl;
             return false;
         }
         c = (a - b) / (a + b);
         return true;
     }
     ```

   - Solution 3: Throw exceptions(C++ feature) 

     ```C++
     float ration(float a, float b) {
         if (fabs(a + b) < FLT_EPSILON) {
             throw "Error ...";
         }
         
         return (a - b) / (a + b);
     }
     ```

     ```C++
     try {
         z = ratio(x, y);
         std::cout << z << std::endl;
     } catch(const char* msg) {
         std::cerr << msg << std::endl;
     }
     ```

### More about Exception

1. Handling Exception

   - A try block can be followed by multiple catch blocks

     ```C++
     float ratio(float a, float b) {
         if (a < 0) {
             throw 1;
         }
         
         if (b < 0) {
             throw 2;
         }
         
         if (fabs(a + b) < FLT_EPSILON) {
             throw "Error ...";
         }
         
         return (a - b) / (a + b);
     }
     ```

     ```C++
     try {
         z = ratio(x, y);
     } catch(const char* msg) {
         // ...
     } catch(int eid) {
         // ...
     }
     ```

2. Stack Unwinding(解开)

   - If an exception is not handling in the function, throw it to the callerr

   - If the caller dose not handle, throw it to the caller of the caller, or until main()

     ```C++
     float ratio(float a, float b) {
         if (a < 0) {
             throw 1;
         }
         
         if (b < 0) {
             throw 2;
         }
         
         if (fabs(a - b) < FLT_EPSILON) {
             throw "Error ...";
         }
         
         return (a - b) / (a + b);
     }
     
     float ratio_wrapper(float a, float b) {
         try {
             return ratio(a, b);
         } catch(int eid) {
             // ...
         }
         
       	return 0;
     }
     
     int main() {
         try {
             z = ratio_warpper(x, y);
         } catch(const char* msg) {
             // ...
         }
         
         return 0;
     }
     ```

3. Catch-all Handler

   - If an exception is not caught, it will reach to the top caller, and terminate the program

   - A catch-all hander can catch all kinds of exceptions

     ```C++
     int main() {
         runSomething0();
         try {
             runSomething1();
         } 
         runSomeOthers();
         catch(...) {
             std::cerr << "Unrecognized Exception" << std::endl;
         }
         
         return 0;
     }
     ```

4. Exceptions and Inheritance

   - If an object is thrown, and its class is derived from another class

   - An exception handler with the base class type can catch the exception

     ```C++
     try {
         throw Derived();
     } catch(const Base& base) {
         std::cerr << "I caught Base" << std::endl;
     } catch(const Derived& derived) {
         // never reach here
         std::cerr << "I caught Deived" << std::endl;
     }
     ```

5. `std::exception`

   - `std::exception` is a class which can be a base class to any exception
   - Function `std::exception::what()` can be overrode to return a C-style string message

6. Exception Specifications and `noexcept`

   - The `noexcept` specifier defines a function which will not throw anything

     ```C++
     void foo() noexcept;	// the function is non-throwing
     ```

### `nothrow`

1. `nothrow new`

   - `std::nothrow` is a constant to select a non-throwing allocation function

     ```C++
     int* p = NULL;
     
     try {	// may throw an exception
         p = new int[length];
     } catch(std::bad_alloc& ba) {
         cerr << ba.what() << endl;
     }
     
     // not throw an exception
     p = new(nothrow) int[length];
     if (p == NULL) {
         // ...
     }
     ```

## WEEK15

---

### Friend Classes

1. `friend` Function

   - A friend function is defined out of the class

     ```C++
     class MyTime {
         public:
         friend MyTime operator+(int m, const MyTime& t);
     };
     
     MyTime operator+(int m, const MyTime& t) {
         return t + m;
     }
     ```

2. `friend` Class

   - A class is a friend of another class

   - The friend class can access all members even private members

   - A friend class can be public, protected and private

     ```C++
     class Sniper {
         private:
         int bullets;
         public:
         Sniper(int bullets = 0): bullets(bullets) {}
         friend class Supplier;
     };
     
     class Supplier {
         int storage;
         public:
         Supplier(int storage = 1000): storage(storage) {}
         bool provide(Sniper& sniper) {
             // bullets is a private member
             if (sniper.bullets < 20) {
                 // ...
             }
         }
     };
     ```

3. `friend` Member Function

   - A single member function of a class is a friend

   - Different from friend functions

   - But very similar to a normal friend function

     ```C++
     class Sniper {
         private:
         int bullets;
         public:
         Sniper(int bullets = 0): bullets(bullets) {}
         friend bool Supplier::provide(Sniper& sniper);
     }
     ```

### Nested Types(内部类型)

1. Nested Enumeration(C++11)

   - `enum DataType` is only used in class Mat, we can put it inside of the Mat

     ```C++
     enum DataType {
         TYPE8U,
         TYPE8S,
         TYPE32F,
         TYPE64F
     };
     
     class Mat {
         private:
         DataType type;
         void* data;
         public:
         Mat(DataType type): type(type), data(NULL) {}
         DataType getType() const {
             return type;
         }
     };
     ```

   - It can be accessed outside of the class, but with the class name scope(作用域) qualifier（`Mat::DataType::TYPE8U`）

     ```C++
     class Mat {
         public:
         enum DataType {
             TYPE8U,
             TYPE8S,
             TYPE32F,
             TYPE64F
         };
         private:
         DataType type;
         void* data;
         public:
         Mat(DataType type): type(type), data(NULL) {}
         DataType getType() const {
             return type;
         }
     };
     ```

2. Nested Classes

   - Nested classes: The declaration of a `class`/`struct` or `union` may appear inside another class

     ```C++
     class Storage {
         public:
         class Fruit {
             string name;
             int weight;
             public:
             Fruit(string name = "", int weight = 0);
             string getInfo();
         };
         
         private:
         Fruit fruit;
         
         public:
         Storage(Fruit f);
     };
     ```

3. Nested Type: Scope

   - Private:
     - Only visible to the containing class
   - Protect:
     - Visible to the containing class and its derived class
   - Public:
     - Visible to the containing class, to its derived classes, and to the outside world

### RTTI and Type Cast Operators

1. Runtime Type Identification(RTTI)

   - We can convert a pointer explicitly to another, even it isn't appropriate

   - How to convert safely?

     ```C++
     class Person;
     class Student: public Person;
     
     Person person("Yu");
     Student student("Sam", "20210212");
     Person* pp = &student;
     Person& rp = student;
     Student* ps = (Student*)&person;	// danger
     ```

2. RTTI and Type Cast Operators

   - Runtime type identification(RTTI)
     - C++ feature
     - The type of an object to be determined during runtime
   - `dynamic_cast` operator: conversion of **polymorphic(多态的) types**
   - `typeid` operator: identify the exact type of an object
   - `type_info` class: the type information returned by the `typeid` operator

3. `typeid`

   - `typeid` operator
     - determine whether two objects are the same type
     - Accept: the name of a class. An expression that evaluates to an object
   - `type_info` class
     - The `typeid` operator returns a reference to a type_info object
     - Defined in the `<typeinfo>` header file
     - Comparing type using the overloaded `==` and `!=` operators

4. `dynamic_cast`

   - It can safely assign the address of an object to a pointer of a particular type

   - Invoke the correct version of a class method (remember virtual functions)

     ```C++
     Person person("Yu");
     Student student("Sam", "20210212");
     Person* pp = NULL;
     Student* ps = NULL;
     
     ps = dynamic_cast<Student*>(&person);	// NULL
     pp = dynamic_cast<Person*>(&student);
     ```

5. More Type Cast Operators

   - `const_cast`

     - Type cast for `const` or `volatile` value

       ```C++
       const int value = 100;
       int* pv = const_cast<int*>(&value);
       (*pv)++;	// value = 100, *pv = 101
       
       int& rv = const_cast<int&>(value);
       rv++;	// value = 100, *pv = 102, rv = 102
       ```

   - `static_cast`

     - It's valid only if `type_name` can be converted implicitly to the same type that expression has, or vice versa
     - Otherwise, the type cast is an error

   - `reinterpret_cast`

     - Convert between types by reinterpreting the underlying bit pattern

       ```C++
       int i = 18;
       float* p1 = reinterpret_cast<float*>(i);	// static_cast will fail
       ```

       


---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/ccppprogramdesign/  

