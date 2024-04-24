# Java基础入门

## 0. HelloWorld

Java语言的特点：

- 特点一：面向对象
	- 两个基本概念：类、对象
	- 三大特性：封装、继承、多态
- 特点二：健壮性
	- 吸收了C/C++语言的优点，但去掉了其影响程序健壮性的部分（如指针、内存的申请与释放等），提供了一个相对安全的内存管理和访问机制。
- 特点三：跨平台性
	- 跨平台性：通过Java语言编写的应用程序在不同的系统平台上都可以运行。“Write once , Run Anywhere”
	- 原理：只要在需要运行 java 应用程序的操作系统上，先安装一个Java虚拟机(JVM Java Virtual Machine) 即可。由JVM来负责Java程序在该系统中的运行。

Java开发环境说明：

- JDK(Java Development Kit——Java开发工具包)
	- JDK是提供给Java开发人员使用的，其中包含了**java开发工具**、**JRE**。所以安装了JDK就不用在单独安装JRE了。其中的开发工具：编译工具(javac.exe)、打包工具(jar.exe)等。
- JRE(Java Runtime Environment——Java运行环境)
	- 包括Java虚拟机(JVM Java Virtual Machine)和Java程序所需的核心类库等，如果想要运行一个开发好的Java程序，计算机中只需要安装JRE即可。

>总的来说：
    JDK = JRE + 开发工具集（例如Javac编译工具等）
    JRE = JVM + Java SE标准类库 

最简单的Java应用程序：

```java
package d01_helloworld;

public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("we will not use 'Hello World!'");
    }
}
```

首先，要明确Java是严格区分大小写的。接下来，逐行解释上面的代码：

1. `package`: 关键字，声名当前代码位于 `d01_helloworld` 包中。
2. `public`: 关键字，访问修饰符，用于控制程序的其他部分对这段代码的访问级别。
3. `class`: 关键字，Java程序中的全部内容都包含在类中。在一个 `.java` 文件中可以有多个类，但声名为 `public` 的类只有一个，且必须与文件名同名。
4. `HelloWorld`: 类名，必须以字母开头，后面可以跟字母和数字的任意组合，长度基本上没有限制，但是不能使用Java保留字。==Java中类名应遵循驼峰命名法(CamelCase)==。
5. `public static void main(String[] args)`: mian方法的签名，固定写法。运行已编译的程序时，Java虚拟机总是从指定类中的main方法的代码开始执行，即程序入口。
6. `{...}`: 代码块。在Jva中，用大括号划分程序的各个部分，任何方法的代码都用 `{` 开始，用 `}` 结束。
7. `System.out.println("we will not use 'Hello World!'");`: 方法体中的一条语句，在Java中，每条语句必须以`;`结尾。这条语句的作用是将一条文本行输出到控制台，即使用`System.out`对象并调用了它的`println()`方法。

编译与运行：

```
源文件：HelloWorld.java ————> 字节码文件：HelloWorld.class ————> 结果
                         |                                |
          编译：javac Helloworld.java             运行：java HelloWorld
```

Java中有三种类型的注释：

- 单行注释
- 多行注释
- 文档注释（Java特有）

```java
// 单行注释

/*
    多行注释。
    多行注释里面不允许有多行注释嵌套。
    对于单行和多行注释，被注释的文字，不会被JVM（java虚拟机）解释执行。
*/

/**
 * @Description: 文档注释
 * 注释内容可以被JDK提供的工具javadoc所解析，生成一套以网页文件形式体现的该程序的说明文档。
 * @Author: ZhangYu
 * @Data: 2024/04/19
 */
```

Java名称命名规范：

- 包名：多单词组成时所有字母都小写：`xxxyyyzzz`
- 类名、接口名：多单词组成时，所有单词的首字母大写：`XxxYyyZzz`
- 变量名、方法名：多单词组成时，第一个单词首字母小写，第二个单词开始每个单词首字母大写：`xxxYyyZzz`
- 常量名：所有字母都大写。多单词时每个单词用下划线连接：`XXX_YYY_ZZZ`


## 1. 数据类型

Java是一种强类型语言，这就意味着必须为每一个变量声明一种类型。在Java中，一共有8种基本类型(primitive type)，其中有4种整型、2种浮点类型、1种字符类型char（用于表示Unicode编码的代码单元），和1种boolean类型。

|类型   |存存储大小   |取值范围             |
|:---:  |:--------: |:------------:     |
|byte   |1字节(8bit)|-128 ~ 127         |
|short  |2字节      |-2^15 ~ 2^15-1     |
|int    |4字节      |-2^31 ~ 2^31-1 (刚超20亿)    |
|long   |8字节      |-2^63 ~ 2^63-1     |
|float  |4字节      |-3.403E38 ~ 3.403E38 (有效位为 6~7位)    |
|double |8字节      |-1.798E308 ~ 1.798E308 (有效位为 15 位) |
|char   |2字节      |-2^15 ~ 2^15-1     |
|boolean|-         |true/false      |

注意：

1. bit（位）是计算机中的最小存储单位；byte（字节）是计算机中基本存储单元。
2. 整型常量默认为 int 型；声明 long 型常量，须后加`l`或`L`。
3. 浮点型常量默认为 double 型；声明float型常量，须后加`f`或`F`。
4. 声明 char 型变量，通常使用一对 `''` 单引号；Java中还允许使用转义字符。
5. 上述8种基本类型都有对应的包装类。

自动类型转换：

- 自动类型转换：容量小的类型自动转换为容量大的数据类型。
- byte、short、char 之间不会相互转换，他们三者在计算时首先转换为 int 类型。
- boolean类型不能与其它数据类型运算。

强制类型转换：

- 强制转换符：`()`，但可能造成精度降低或溢出，格外要注意。
- boolean 类型不可以转换为其它的数据类型。

## 2. 变量与常量

### 2.1. 变量

声名变量：

```java
/*
    格式：“类型 变量名;”
*/
double salary;
int vacationDays;
long earthPopulation;
boolean done;

/* 
    可以在一行中声明多个变量。不过，不提倡使用这种风格。
    逐一声明每一个变量可以提高程序的可读性。
*/
int i,j;
```

变量初始化：

声明一个变量之后，必须用赋值语句对变量进行显式初始化，千万不要使用未初始化的变量的值。

```java
// 变量的声名与初始化分别进行
int vacationDays;
vacationDays 12;

// 变量的声明和初始化放在同一行中
int vacationDays = 12;
```

在Java中，变量的声明尽可能地靠近变量第一次使用的地方，这是一种良好的程序编写风格。

>注意：从Java10开始，对于局部变量，如果可以从变量的初始值推断出它的类型，就不再需要声明类型。只需要使用关键字 `var` 而无须指定类型：
`var vacationDays = 12;     // vacationDays is an int`
`var greeting = "Hello";    // greeting is a String`

### 2.2. 常量

在Java中，利用关键字`final`指示常量，表示这个变量只能被赋值一次。一旦被赋值之后，就不能够冉更改了。习惯上，常量名使用全大写。

```java
public class Constants {
    public static void main(String[]args) {
        final double CM_PER_INCH = 2.54;
        System.out.println(CM_PER_INCH);
    }
}
```

希望某个常量可以在一个类的多个方法中使用，通常将这些常量称为类常量(class constant)。可以使用关键字`static final`设置一个类常量。

```java
public class Constants2 {

    final double CM_PER_INCH = 2.54;

    public static void main(String[]args) {
        System.out.println(CM_PER_INCH);
    }
}
```

>注意：const是Java保留的关键字，但目前并没有使用。在Java中，必须使用final定义常量。

## 3. 运算符

### 3.1. 算术运算符

| 运算符 |         操作说明       |   示范    |  结果   |
| :----: | :------------------: | :-------: | :-----: |
|  `+`   |  加\正号\字符串连接  |    +3     |    3    |
|  `-`   |       减\负号        |  a=4;-a   |   -4    |
|  `*`   |          乘          |   3\*4    |   12    |
|  `/`   |          除          |   12/4    |    3    |
|  `%`   |         取模         |    7%5    |    2    |
|  `++`  | 前加加：先运算后取值 | a=2;b=++a | b=3;a=3 |
|  `++`  | 后加加：先取值后运算 | a=2;b=a++ | b=2;a=3 |
|  `--`  | 前减减：先取值后运算 | a=2;b=--a | b=1;a=1 |
|  `--`  | 后减减：先取值后运算 | a=2;b=a-- | b=2;a=1 |

对于一些复杂的数学运算，可以借助于`Math`类中的数学函数来实现。下面列举了一些常见的数学函数方法：

```java
// 根号
Math.sqrt
// 幂函数
Math.pow
// 三角函数
Math.sin
Math.cos
Math.tan
Math.atan
Math.atan2
// 指数函数以及它的反函数
Math.exp
Math.log
Math.1og10
// pi 和 e 的近似值
Math.PI
Math.E
```

### 3.2. 比较运算符

|    运算符    |        操作说明      |          示范          | 结果  |
| :----------: | :----------------: | :--------------------: | :---: |
|     `==`     |       相等于       |         4\=\=3         | false |
|     `!=`     |       不等于       |         4\!\=3         | true  |
|     `<`      |        小于        |          4<3           | false |
|     `>`      |        大于        |          4>3           | true  |
|     `<=`     |      小于等于      |         4\<\=3         | false |
|     `>=`     |      大于等于      |         4\>\=3         | true  |
| `instanceof` | 检查是否是类的对象 | "hi" instanceof String | true  |

此外，Java支持三元操作符`?:`。

```java
/*
    如果条件为true，结果位表达式1的值；
    否则结果为表达式2的值。
*/
var result = condition ? expresion1 : expression2;
```

### 3.3. 逻辑运算符

| 符号 | 操作说明 | 示例 | a=true;<br/>b=true | a=true;<br/>b=false | a=false;<br/>b=true | a=false;<br/>b=false |
|:---:|:-------:|:---:| :----------------: | :-----------------: | :-----------------: | :------------------: |
| `&` |  逻辑与  | a&b |    true      |     false      |     false      |      false      |
| `&&`|  短路与 | a&&b |    true      |     false      |     false      |      false      |
| `\|`  |  逻辑或 | a\|b |    true      |      true      |      true      |      false      |
| `\|\|` | 短路或 | a\|\|b |    true      |      true      |      true      |      false      |
| `!` |   非   |  !a  |   false     |     false      |      true      |      true       |
| `^` |  异或  |  a^b  |   false     |      true      |      true      |      false      |

>`&` 与 `&&`的区别：
>- 单`&`：左边无论真假，右边都进行运算；
>- 双`&`：如果左边为真，右边参与运算，如果左边为假，那么右边不参与运算。
>- `||`与`|`的关系与上同理。

### 3.4. 位运算符

| 运算符 |         操作说明       |  示例   |   结果   |
| :----: | :------------------: | :-----: | :------: |
|  `<<`  |         左移         | `3<<2`  | 3×2×2=12 |
|  `>>`  |         右移         | `3>>1`  |  3/2=1   |
| `>>>`  |      无符号右移      | `3>>>1` |  3/2=1   |
|  `&`   | 按二进制位进行与运算 |  `6&3`  |    2     |
|   \|   | 按二进制位进行或运算 |  6\|3   |    7     |
|  `^`   |       异或运算       |  6\|3   |    5     |
|  `~`   |       取反运算       |  `~6`   |    -7    |

### 3.5. 赋值运算符

- 符号：`=`
- 扩展赋值运算符：`+=, -=, *=, /=, %=, <<=, >>=, ...`

## 4. 字符串

从概念上讲，Java字符串就是Unicode字符序列。Java没有内置的字符串类型，而是在标准Java类库中提供了一个预定义类，叫做String。每个用双引号括起来的字符串都是String类的一个实例。

String 类的位置：`java.lang.String`，下面列举了常见的 String API。

```java
char charAt(int index)  //返回给定位置的代码单元。
int codePointAt(int index) //返回从给定位置开始的码点。
int offsetByCodePoints(int startIndex,int cpCount) //返回从startIndex码点开始，cpCount个码点后的码点索引。
int compareTo(String other) //照字典顺序进行比较。
IntStream codePoints()  //将这个字符串的码点作为一个流返回。
new String(int[]codePoints,int offset,int count)    //用数组中从offset开始的count个码点构造一个字符串。
boolean empty() //字符串是否为空。
boolean blank() //字符串是否由空格组成。
boolean equals(Object other)    //字符串内容是否相同。
boolean equalsIgnoreCase(String other)  //同上，忽略大小写。
boolean startsWith(String prefix)   //字符串是否以prefix开头。
boolean endsWith(String suffix) //字符串是否以suffix结尾。
int indexof(String str)
int indexof(String str,int fromIndex)
int indexof(int cp)
int indexof(int cp,int fromIndex)   //返回与字符串str或码点cp匹配的第一个子串的开始位置。从索引0或fromIndex开始匹配。如果在原始字符串中不存在str，则返回-1。
int lastIndexof(String str)
int lastIndexof(String str,int fromIndex)
int lastindexof(int cp)
int lastindexof(int cp,int fromIndex)   //返回与字符串str或码点cp匹配的最后一个子串的开始位置。从原始字符串末尾或fromIndex开始匹配。
int length()    //返回字符串代码单元的个数。
int codePointCount(int startIndex,int endIndex) //返回startIndex和endIndex-1之间的码点个数。
String replace(CharSequence oldString,CharSequence newString)   //用newString代替原始字符串中所有的oldString。可以用String或StringBuilder对象作为CharSequence参数。
String substring(int beginIndex)
String substring(int beginIndex,int endIndex)   //返回一个子字符串，从beginIndex到字符串末尾或endIndex-1。
String toLowercase()    //将原始字符串中的大写字母改为小写
String toUpperCase()    //将原始字符串中的所有小写字母改成大写字母。
String trim()   //删除原始字符串头部和尾部小于等于U+0020的字符。
String strip()  //删除原始字符串头部和尾部的空格。
String join(CharSequence delimiter,CharSequence...elements) //用给定的定界符连接所有元素。
String repeat(int count)    //将当前字符串重复count次。
```

字符串拼接：Java语言允许使用`+`号拼接两个字符串。当将一个字符串与一个非字符串的值进行拼接时，后者会转换成字符串。

**不可变性**：String类没有提供修改字符串中某个字符的方法，由于不能修改字符串中的单个字符，所以在Java文档中将String类对象称为是不可变的。
>字符串常量池：各种字符串存放在公共的存储池中。字符串变量指向存储池中相应的位置。如果复制一个字符串变量，原始字符串与复制的字符串共享相同的字符。

有些时候，采用字符串拼接的方式构建新字符串，效率会比较低。每次拼接字符串时，都会构建一个新的String对象，既耗时，又浪费空间，使用StringBuilder类就可以避免这个问题的发生。

如果需要用许多小段的字符串来构建一个字符串，那么应该按照下列步骤进行：

1. 首先，构建一个空的字符串构建器：
   ```java
   StringBuilder builder = new StringBuilder();`
   ```
2. 当每次需要添加一部分内容时，就调用append方法：

    ```java
    builder.append(ch);     //appends a single character
    builder.append(str);    //appends a string
    ```

3. 在字符串构建完成时就调用toString方法，将可以得到一个String对象，其中包含了构
建器中的字符序列。
    ```java
    String completedString = builder.toString();
    ```

>注释：StringBuilder类在Java5中引入。这个类的前身是StringBuffer，它的效率稍有些低，但允许采用多线程的方式添加或删除字符。如果所有字符串编辑操作都在单个线程中执行，则应该使用StringBuilder。

StringBuilder和StringBufer这两个类的API是一样的，如下所示。(`java.lang.*`)

```java
StringBuilder() //空参构建器。
int length()    //返回构建器或缓冲器中的代码单元数量。
StringBuilder append(String str)    //追加一个字符串并返回this。
StringBuilder append(char c)    //追加一个代码单元并返回this。
StringBuilder appendCodePoint(int cp)   //追加一个码点，并将其转换为一个或两个代码单元并返回ths。
void setCharAt(int i,char c)    //将第i个代码单元设置为c。
StringBuilder insert(int offset,String str) //在offset位置插入一个字符串并返回this。
StringBuilder insert(int offset,char c) //在offset位置插入一个代码单元并返回this。
StringBuilder delete(int startIndex,int endIndex)   //删除偏移量从startIndex到endIndex-1的代码单元并返回this。
String toString()   //返回一个与构建器或缓冲器内容相同的字符串。
```

## 5. 输入与输出

有时，程序需要接受外部输入，并适当地格式化输出。我们的目标是要使用基本的控制台来实现输入输出。

### 5.1. 读取输入

要想通过控制台进行输入，首先需要构造一个与“标准输入流”System.in关联的Scanner对象。

```java
Scanner in = new Scanner(System.in);
```

Scanner类的各种读取输入的方法如下：(`java.util.Scanner`)

```java
Scanner(InputStream in) //用给定的输入流创建一个Scanner对象。
String nextLine()   //读取输入的下一行内容。
String next()   //读取输入的下一个单词（以空格作为分隔符）。
int nextInt()   //读取并转换下一个表示整数的字符序列。
double nextDouble() //读取并转换下一个表示浮点数的字符序列。
boolean hasNext()   //检测输入中是否还有其他单词。
boolean hasNextInt()    //是否还有下一个表示整数的字符序列。
boolean hasNextDouble() //是否还有下一个表示浮点数的字符序列。
```
>注意：因为输入是可见的，所以Scanner类不适用于从控制台读取密码。Java6特别引入了Console类来实现这个目的。

```java
Console cons = System.console();
String username = cons.readLine("User name:");
char[]passwd = cons.readPassword("Password:");  //返回的密码存放在一个字符数组中
```

### 5.2. 格式化输出

Java5沿用了C语言函数库中的printf方法，通过`System.out.printf(String format,Object... args);`调用，例如：

```java
System.out.printf("%8.2f", x);
System.out.printf("Hello, %s. Next year, you'll be %d !", name, age);
```

==格式说明符==以%字符开始，打印时会被相应的参数值替换。格式说明符尾部的==转换符==指示要格式化的数值的类型，用于printf的转换符如下所示：

|转换符         |类型                         |示例           |
|--------------|----------------------------|---------------|
|**d**         |十进制整数                    |159            |
|x             |十六进制整数                  |9f             |
|0             |八进制整数                    |237            |
|**f**         |定点浮点数                    |15.9           |
|e             |指数浮点数                    |1.59e+01       |
|9             |通用浮点数(e和f中较短的一个)    | -             |
|a             |十六进制浮点数                |0x1.fccdp3     |
|**s**         |字符串                       |Hello          |
|c             |字符                        |H              |
|b             |布尔                        |true           |
|h             |散列码                       |42628b2        |

另外，还可以指定控制格式化输出外观的各种==标志==。用于printf的标志如下所示：

|标志            |目的                              |示例             |
|---------------|----------------------------------|----------------|
|+              |打印正数和负数的符号                 |+3333.33         |
|空格            |在正数之前添加空格                   |\| 3333.33 \|    |
|0              |数字前面补0                         |003333.33        |
|-              |左对齐                             |\|3333.33 \|     |
|\(             |将负数括在括号内                     |(3333.33)        |
|,              |添加分组分隔符                       |3,333.33         |
|#(对于f格式)    |包含小数点                           |3,333.            |
|#（对于x或0格式) |添加前缀x或0                         |0xcafe           |
|$              |指定要格式化的参数索引。例如，`%1$d %1$x` 将以十进制和十六进制格式打印第1个参数   |159 9F  |
|<              |格式化前面说明的数值。例如，`%d%<x` 将以十进制和十六进制打印同一个数值           |159 9F  |

### 5.3. 文件输入与输出

使用Scanner对象，读取文件中的内容：

```java
Scanner in = new Scanner(Path.of("myfile.txt"), StandardCharsets.UTF 8);
```

使用PrintWriter对象，向文件中写入内容：

```java
PrintWriter out =  new PrintWriter("myfile.txt", StandardCharsets.UTF 8);
```
上述类涉及的包位置：`java.util.Scanner`，`java.nio.charset.StandardCharsets`，`java.io.PrintWriter`，`java.nio.file.Path`

>注意：如果用一个不存在的文件构造一个Scanner，或者用一个无法创建的文件名构造一个Printwriter，就会产生异常。

## 6. 流程控制

### 6.1. 条件语句

执行体仅有一条语句时：

```java
if (condition) statement;
if (condition) statement1; else statement2;
```

执行体为复合语句时：

```java
if (condition) {
    statement1;
    statement2;
} else if {
    statement3;
} else {
    statement4;
}
```

### 6.2. 循环

while循环：

```javss
while (condition) {
    statements;
}
```

>while循环语句在最前面检测循环条件。因此，循环体中的代码有可能一次都不执行。

do while 循环：

```java
do {
    statements;
} while (condition);
```

>do-while循环语句先执行语句（通常是一个语句块），然后再检测循环条件。因此循环体至少执行一次。

上面两种while循环中循环体的执行次数是未知的，而下面介绍的for循环是一种确定循环，由一个计数器或类似的变量控制迭代次数。

for循环：

```java
for (初始化计数器; 检查循环条件; 更新计数器) {
    循环体;
}
```

for each 循环：

Java有一种功能很强的循环结构，可以用来依次处理数组（或者其他元素集合）中的每个元素，而不必考虑指定下标值。

```java
for (int element : Iterable) {
    System.out.println(element);
}
```

### 6.3. 多重选择switch

switch语句将从与选项值相匹配的case标签开始执行，直到遇到break语句，或者执行到switch语句的结束处为止。如果没有相匹配的case标签，而有default子句，就执行这个子句。

```java
switch (choice) {
    case 1:
        statement1;
        break;
    case 2:
        statement2;
        break;
    case 3:
        statement3;
        break;
    case 4:
        statement4;
        break;
    default:
        statement5;
        break;
}
```

case标签可以是：

- 类型为char、byte、short或int的常量表达式。
- 枚举常量。
- 从Java7开始，case标签还可以是字符串字面量。

### 6.4. 中断控制流程

- `break`：跳出当前循环的代码块，执行后续代码。
- `continue`：跳出本次循环，继续下一轮循环。

与C++不同，尽管Java将goto作为保留字，但实际上并没有使用它，因此不支持使用goto跳出循环。因此，Java设计了带标签的break和continue，以此来支持这种程序设计风格。

```java
label1:
while (...) {   //外层带有标签的循环
    for (...) { //内层循环
        statements;
        if (condition) {
            break label1;   //直接跳出外层循环
        }
    }
}
```

## 7. 大数

在处理金融等精度要求高的情景时，浮点数精度不能够满足需求，那么可以使用BigInteger和BigDecimal两个类，实现任意精度的运算。

位置：`java.math.BigInteger`，`java.math.BigDecimal`。

下面是以BigDecimal为例，列举了常见的方法：

```java
BigDecimal add(BigDecimal other)        //和
BigDecimal subtract(BigDecimal other)   //差
BigDecimal multiply(BigDecimal other)   //积
BigDecimal divide(BigDecimal other)     //商
BigDecimal divide(BigDecimal other,RoundingMode mode)   //商，第二个参数mode指定模式。如：RoundingMode.HALF_UP是四舍五入（即，0到4舍去，5到9进位）。
int compareTo(BigDecimal other)     //比较
static BigDecimal valueof(long x)           //返回值等于×的一个大实数。
static BigDecimal valueof(long x,int scale) //返回值等于x/10^scale的一个大实数。
```

>注意：若使用new来构造一个大数对象时，应使用带字符串参数的构造器。

## 8. 数组

数组是一种数据结构，用来存储同一类型值的集合。通过一个整型下标（index，称索
引）可以访问数组中的每一个值。

### 8.1. 声明与初始化

```java
int[] a;    //仅声明
int[] a = new int[100]  //声明并初始化，初始化值为默认值
var a = new int[100];   //使用类型推断

int[] a = new int[4]{1, 2, 3, 4};   //在声明时提供初始值
int[] a = {1, 2, 3, 4};     //在声明时提供初始值，可以简写

a = new int[] {5, 6, 7, 8};     //匿名数组，直接赋值给已有的形同数组类型
```

>注意：一旦创建了数组，就不能再改变它的长度。Java允许长度为0的数组，与null数组不同。

### 8.2. 访问数组元素

根据索引访问数组中的元素：`int n = a[2];`，注意索引从 0 开始，到 len-1 结束。

遍历数组：

```java
for (int i = 0; i < a.length; i++) {
    System.out.println(a[i]);
}
```

此外，可以使用 for each 循环来遍历数组中的元素。

```java
for (int i : a) {
    System.out.println(i);
}
```

### 8.3. 数组拷贝

浅拷贝：

```java
int[] oldNums = new int[5];
int[] newNums = oldNums;
//此时，oldNums和newNums指向内存中的中一块区域
```

深拷贝：

```java
int[] oldNums = new int[5];
int[] newNums = Arrays.copyOf(oldNums, oldNums.length);
//此时，newNums为一个新的数组，不过他存储的值和olsNums值相同
```

其中，`java.util.Arrays` 为数组的工具类。下面列举了Arrays中常见的方法：

```java
/*
    数组元素类型xxx可以是int、long、short、char、byte、boolean、float或double。
*/
static String toString(xxx[]a)  //返回包含中元素的一个字符串。
static xxx[] copyof(xxx[]a,int end) //数组拷贝，其长度为end，数组元素为a的值。
static xxx[]copyOfRange(xxx[]a,int start,int end)   //数组拷贝，其长度为end-start。
static void sort(xxx[]a)    //排序。
static int binarySearch(xxx[]a,xxx v)   //使用二分查找算法在有序数组a中查找值V。如果找到v,则返回相应的下标；否则，返回一个负数值r。-r-1是v应插入的位置（为保持a有序）。
static int binarySearch(xxx[]a,int start,int end,xxx v) //在start和end范围内查找。
static void fill(xxx[]a,xxx v)  //将数组的所有数据元素设置为V。
static boolean equals(xxx[]a,xxx[]b)    //如果两个数组大小相同，并且下标相同的元素都对应相等，返回tue。
```

### 8.4. 数组排序

要想对数值型数组进行排序，可以使用Arrays类中的sort方法：

```java
int[] a = new int[5] {4, 5, 2, 3 ,1};
Arrays.sort(a); //使用了优化的快速排序(QuickSort)算法
```
自定义排序：

- 自然排序：类实现了 `java.lang.Comparable` 接口，重写compareTo()的规则。
- 定制排序：构造实现`java.util.Comparator`接口的类对象，重写compare()方法。

在上述的Arrays.sort()方法中，默认使用升序排序，如需自定义排序规则，比如按照降序排列，则可以考虑上述两种自定义排序方式。

首先，自定义排序的目标是对象，因此我们需要将int类型转换为包装类Integer。

其次，对于Java的内置类，我们无法直接修改其代码，因此自然排序不适用，只能考虑使用定制排序。

```java
/*
    方式一：新建一个类，实现Comparator对象
 */
class MyComparator implements Comparator<Integer>{
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2-o1;
    }
}
Comparator myComparator = new MyComparator();
Arrays.sort(arr, myComparator);

/*
    方式二：使用匿名类
 */
Arrays.sort(arr, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2-o1;
    }
});
```

补充，自然排序的使用方法：

```java
/*
    比较自定义的类的大小
*/
class User implements Comparable<User>{
    Integer age;
    String name;
    //...
    @Override
    public int compareTo(User o) {
        return this.age.compareTo(o.age);
    }
}

//此时，对List<User>等对象进行排序时，将按照User中的age属性进行排序。
```

### 8.5. 多维数组

多维数组将使用多个下标访问数组元素，它适用于表示表格或更加复杂的排列形式。Java实际上没有多维数组，只有一维数组。多维数组被解释为“数组的数组”。

不规则数组：

```java
// 声明与初始化
int[][] odds = new int[row][];

for (int i = 0; i < row; i++) {
        odds = new int[i + 1];
}

// 遍历
for (int i = 0; i < odds.length; i++) {
    for (int j = 0; j < odds[i].length; j++) {
        System.out.print(odds[i][j]);
    }
    System.out.println();
}

// 增强for循环遍历
for (int[] odd : odds) {
    for {int num : odd} {
        System.out.print(num);
    }
    System.out.println();
}
```