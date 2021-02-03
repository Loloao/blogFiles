# Java 基础知识

## 基本类型

- 字符串不是基本类型，而是引用类型

- 浮点型可能只是一个近似值，而不是一个精确值

- 数据范围与字节数不一定相关

- 浮点数默认类型是`float`，整型默认是`int`

- 对于类型`float`和`long`类型来说，后缀`F`和`L`不要丢弃

- 如果使用`byte`或是`short`类型的变量，右边不能超过左边的范围

- `byte/char/short`三种类型在运算时，会首先转换为`int`处理

  ```java
  char A = 'A';
  System.outPrintln(A + 1); // 输出 66，'A' 被当做二进制处理
  
  byte num1 = 40;
  byte num2 = 50;
  byte sum = num1 + num2; // 报错，提示为不能 int 转 byte
  int sum = num1 + num2; // 正确
  byte sum = (byte) (num1 + num2);
  ```

- 对于`byte/char/short`三种类型，如果右侧类型没有超过左边范围，编译器会为它们补上一个`(byte)(char)(short)`，即自动补上强制类型转换，如果超过，直接报错

- 给变量进行赋值的时候，如果右侧的表达式当中全是常量 ，没有任何变量，那么编译器建会直接将若干个常量表达式计算的结果赋予变量

  ```java
  short a = 5;
  short b = 8;
  short result = a + b; // 上文提到，报错
  short result = 5 + 8; // 正确
  ```


## 函数

- 函数重载与下列因素相关
  1. 参数个数不同
  2. 参数类型不同
  3. 参数的多类型顺序不同
- 函数重载与下列因素无关
  1. 与参数的名称无关
  2. 与方法的返回值类型无关

## 数组

- 数组的特点

  1. 数组是一种引用数据类型

  2. 数组当中的多个数据，类型必须统一
  3. 数组的长度在程序运行期间不可改变

- 数组的两种常见的初始化方式

  1. 动态初始化（指定长度）`int[] strArr = new int[2];`

  2. 静态初始化（指定内容）

     ```java
     int[] strArr = new int[] {5, 15, 25};
     // 省略写法
     int[] arrayA = { 5, 15, 25 };
     ```

- 直接打印数组名称，得到的是数组对应的内存地址哈希值

  ```java
  int[] array = { 10, 20, 30 };
  //输出 [I@75412c2f [ 表示数组，I 表示 int 类型，75412c2f 表示 16 进制
  System.out.println(array); 
  ```

- 使用动态初始化数组的时候，其中的元素将会拥有一个默认值。规则如下：

  - 如果是整数，默认为`0`
  - 如果是浮点类型，默认为`0.0`
  - 如果是字符类型，默认为`\u0000`
  - 如果是布尔类型，默认为`false`
  - 如果是引用类型，默认为`null`

  > 静态初始化其实也有默认值的过程，只不过系统自动马上将默认值替换成了大括号当中的具体数值

- Java 的内存要划分成为 5 个部分

  - 栈`stack`：存放的都是方法中的局部变量，一旦超出作用域，立刻从栈内存中消失。方法的运行一定要在栈当中运行
  - 堆`Heep`：凡是`new`出来的东西，都在堆当中，堆内存里的东西都有一个地址值：16进制。堆内存里面的数据，都有默认值
  - 方法区`Method Area`：存储`.class`相关信息，包含方法的信息
  - 本地方法栈`Native Method Stack`：与操作系统相关
  - 寄存区`pc Register`：与 CPU 相关

- 数组的变量只是在栈中只是存一个地址值，通过这个地址值在堆中找变量的存储地址，也就是`new`之后的部分，所以直接打印数组就是地址值。索引也是地址值，会通过索引值到内存中找到对应地址并获取值
- 数组索引越界异常，当需要读取超过数组长度的索引时，会发生越界异常`ArrayIndexOutOfBoundsException`
- 空指针异常，也就是声明了变量但是没有进行初始化，它会被初始化为`null`，此时使用索引调用会产生空指针异常`NullPointerException`

## 类

- 成员变量如果没有赋值，会有默认值，而局部变量没有
- 当方法的局部变量和成员变量重名的时候，优先使用局部变量，此时可以使用`this`来访问成员变量
- 构造方法是专门用来创建对象的方法，当我们通过关键字`new`来创建对象时，其实就是在调用构造方法，有几个注意事项
  1. 构造方法的名称必须和所在类名称完全一样，大小写也是一样
  2. 构造方法不要写返回值类型，连`void`都不写
  3. 构造方法不能`return`一个具体的返回值
  4. 如果没有编写任何构造方法，那么编译器就会默认赠送一个构造方法，没有参数、方法什么事都不做
  5. 一旦编写了至少一个构造方法，编译器就不再赠送
  6. 构造方法也可以重载
- 导包
   - `import 包路径.类名称`
   - 如果需要使用的目标类和当前类在同一个包下，则可以省略导包语句不写
   - 只有`java.lang`包下的内容不需要导包，其他的包都需要
- 匿名对象就是只有右边的`new`对象，而没有左边的赋值语句。匿名对象只能使用一次

## ArrayList

- 可以装载一个泛型，泛型只能是引用类型，而不能是基本类型。从`JDK1.7+`开始，尖括号可以不写内容，但是尖括号不能省略。但是左边的赋值语句必须写
- 如果想向集合中存储基本类型数据，必须使用基本类型的包装类，包装类都位于`java.lang`包下
- 从`JDK1.5+`开始，支持自动装箱（基本类型-->引用类型），自动拆箱（引用类型-->基本类型）
- 对于`ArrayList`来说，直接打印的不是地址值，而是内容，如果内容为空，则为`[]`
- `public boolean add(E e)`向集合中添加元素，参数的类型和泛型一致。返回值表示添加动作是否成功。对于`ArrayList`来说，`add`操作一定是成功的，对于其他集合不一定是成功的
- `public E get(int index)`向集合中获取元素，参数是索引值，返回值就是对应位置的元素
- `public E remove(int index)`删除元素
- `public int size()`获取集合的尺寸长度

## String
- Java 中的所有字面量值都作为`String`类的实例实现
- 字符串的特点
  1. 字符串是常量，创建之后不能更改
  2. 字符串是可以共享使用的
  3. 字符串效果上是`char[]`字符数组，但底层原理是`byte[]`字节数组
- 创建字符串的 3 + 1 种方式
  1. `public String()`创建一个空白字符串，不包含任何内容
  2. `public String(char[] array)`根据字符数组来创建字符串，但底层依然会转换成`byte[]`进行保存
  3. `public String(byte[] array)`根据字节数组来创建字符串
  4. 直接用双引号创建
- `String`的常量池，程序中直接写上的双引号字符串，就在字符串常量池中。字符串常量池是在堆当中的，其中保存的就是字符串字节数组的地址值。直接创建的字符串会在常量池中重复利用同一个地址值
  ```java
  String str1 = 'abc';
  String str2 = 'abc';

  char[] charArray = char[] {"a", "b", "c"};
  String str3 = new String(charArray);

  System.out.println(str1 == str2); // true
  System.out.println(str1 == str3); // false
  System.out.println(str2 == str3); // false
  ```
- 字符串的比较相关方法
  1. `public boolean equals(Object obj)`参数可以是任何对象，只有参数是一个字符串且内容相同才会给`true`，不管创建方法是什么。如果比较常量和变量，推荐把常量写在前面，避免变量为`null`
  2. `public boolean equalsIgnoreCase(String str)`忽略大小写进行内容比较
- 字符串获取的相关方法
  1. `public int length()`获取字符串当中含有的字符个数
  2. `public String concat(String str)`将当前字符串和参数字符串拼接成为返回值新的字符串
  3. `public char charAt(int index)`获取指定位置的单个字符
  4. `public int indexOf(String str)`查找参数字符串在本字符串首次出现的索引位置
- 字符串的截取方法
  1. `public String substring(int index)`截取从参数位置一直到字符串尾，返回新字符串
  2. `public String substring(int begin, int end)`截取从`begin`到`end`位置中间的字符串，`[begin, end)`也就是包含左边，不包含右边
- 字符串的转换方法
  1. `public char[] toCharArray()`将当前字符串拆分成为字符数组作为返回值
  2. `public byte[] getBytes()`获取当前字符串的底层字节数据
  3. `public String replace(CharSequence oldString, CharSequence newString)`将所有出现的老字符串替换为老字符串，返回新字符串。`CharSequence`是一个接口，表示接受字符串类型
- 字符串的分割方法
  1. `public String[] split(String regex)`按照参数规则将字符串切割成为若干部分，注意参数是一个正则表达式
