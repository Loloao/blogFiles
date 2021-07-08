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
- 静态方法只能访问静态变量，不能使用`this`。因为在内存中是先有的静态内容，后有的非静态内容
- 在方法区中有独立的静态方法区，也就是和类在一起的，只和类有关系
- 静态代码块只会在第一次用到类时执行一次，典型用法就是一次性地对静态成员变量进行赋值
  ```java
  public class Person {
    static {
      System.println("静态代码执行")
    }
  }
  ```
### 继承
- 子类方法的返回值类型必须小于等于父类的返回值范围
- 子类方法的权限必须大于等于父类方法的权限修饰符`public > protect > (default) > private`
- 继承关系中，父子类构造方法的访问特点
  1. 子类构造方法有一个默认隐含的`super()`调用，所以会先调用父类的构造方法再调用子类
  2. 可以通过`super`关键字来子类构造调用父类重载构造
  3. `super`的父类构造调用，必须是子类构造方法的第一个语句，不能多次调用`super`
- `super`关键字在内存中会保存对父类的引用，在堆中，子类内容会包含一个父类内容
- Java 继承的三个特点
  1. 一个类的直接父类只能有一个
  2. Java 可以多级继承
  3. 一个父类可以有多个子类
- 抽象类的子类必须覆盖重写抽象类中的所有抽象方法，除非子类也是抽象类
- 代码当中体现多态性就是：父类引用指向子类对象，口诀：编译看左边，运行看右边
  1. 直接通过对象名称访问成员变量：看等号左边是谁，优先用谁，没有则向上找，子类不可以覆盖重写变量
  2. 间接通过成员方法访问成员变量：看该方法属于谁，优先用谁，没有则向上找
  ```java
  Fu obj = new Zi();
  System.out.println(obj.num); // 父类的 10
  obj.showNum(); // 子类没有覆盖重写，则返回 10
  obj.showNum(); // 子类如果覆盖重写，则返回 20
  obj.methodZi(); // 编译看左，这里编译会报错，因为 obj 是 Fu 类，Fu 类没有 methodZi 方法
  ```
- 对象的向上转型，其实就是多态写法。`父类名称 对象名 = new 子类名称();`，其实就是右侧创建一个子类对象，把它当做父类使用。向上转型一定是安全的。但一旦转为父类，就无法使用子类的特别方法
- 对象的向下转型，其实是一个*还原*的操作。`子类名称 对象名 = （子类名称）父类对象;`，将父类对象还原为子类对象
  ```java
  Animal animal = new Cat()
  Cat cat = (Cat) animal; // 本来是猫，还原为猫。必须保证本来创建的时候，就是猫，才能向下转型
  Dog dog = (Dog) animal: // 本来是猫，非要还原为狗，错误写法，编译不会报错，但是运行出现异常
  ```
- 使用`instanceof`进行类型判断，如何知道一个父类引用的对象，本来是什么子类
  ```java
  if (animal instanceof Dog) {
    Dog dog = (Dog) animal;
    dog.watchHouse();
  }
  ```
### `final`关键字
  有四种用法，不过对于类和方法来说`abstract`和`final`不能同时使用
  1. 可以用来修饰一个类，`public final class 类名称`，当前这个类不能有任何子类
  2. 可以用来修饰一个方法，`修饰符 final 返回值类型 方法名称`当前这个方法就是最终方法，不能对它进行覆盖重写
  3. 可以用来修饰一个局部变量，一旦使用`final`关键字对局部变量进行修饰，就不能进行更改，只要有唯一一次赋值即可
  4. 可以用来修饰一个成员变量，也是不可变，但是要注意
    1. 由于有默认值，必须手动赋值
    2. 对于`final`的成员变量，要么直接赋值，要是使用构造方法赋值
- Java 中有四种权限修饰符：`public > protected > (default) > private`
  - 同一个类 yes yes yes yes
  - 同一个包 yes yes yes no
  - 不同包子类 yes yes no no
  - 不同包非子类 yes no no no
### 内部类
  分为
  1. 成员内部类，调用它有两种方法
    - 间接方式：在外部类的方法中，使用内部类；然后`main`只是调用外部类的方法
    - 直接方式：类名称 对象名 = new 类名称();外部类名称.内部类名称 对象名 = new 外部类名称().new 内部类名称();
      ```java
      Body.Heart heart = new Body().new Heart();
      ```
  2. 局部内部类（包含匿名内部类），如果一个类定义在一个方法内部，那么就是局部内部类。同时不能进行
- 内部类的同名对象访问，通过`this`访问，如果出现了重名现象，那么格式是：外部类名称.this.外部类成员变量名
- 局部内部类如果希望访问所在方法的局部变量，那么这个局部变量必须是`有效 final 的`，可以直接给它加上`final`或是保证它事实不变。原因：
  1. new 出来的对象是在堆内存中
  2. 局部变量是跟着方法走的，在栈内存当中
  3. 方法运行结束之后，立刻出栈，局部变量就会立刻消失
  4. new 出来的对象会在堆中一直存在，知道垃圾回收为止
### 匿名类，
  如果接口的实现类（或是弗雷德子类）只需要使用唯一的一次，就可以省略这个类的定义
  - 匿名内部类的定义格式`接口名称 对象名 = new 接口名称() { 覆盖重写所有抽象方法 }`
  - 注意事项：
    1. 匿名内部类，在创建对象的时候，只能使用唯一一次。如果希望多次 new 的话，只能创建一个类
    2. 在使用匿名内部类创建了匿名对象的时候只能调用唯一一次
    3. 匿名内部类是省略了`实现类/子类名称`，但匿名对象是省略了`对象名称`
### 包装类
基本数据类型的数据使用起来方便但是没有对应的方法操作这些数据
- 包装对象
  - `Integer I = new Integer(1)`      
  - `Integer I = Integer.valueOf(4)`
- 拆箱
  - `int num = i.intValue()`
- 自动装箱和拆箱
  - `Integer I = 1`
  - `I = I + 1`
除了`Character`类之外，所有包装类都会有`parseXxx`静态方法将字符串类型转换为对应的基本类型

## ArrayList

- 可以装载一个泛型，泛型只能是引用类型，而不能是基本类型。从`JDK1.7+`开始，尖括号可以不写内容，但是尖括号不能省略。但是左边的赋值语句必须写
- 如果想向集合中存储基本类型数据，必须使用基本类型的包装类，包装类都位于`java.lang`包下，增删数组速度慢是因为每次增删都会创建一个新数组改变长度后复制原数组并进行增删
- 从`JDK1.5+`开始，支持自动装箱（基本类型-->引用类型），自动拆箱（引用类型-->基本类型）
- 对于`ArrayList`来说，直接打印的不是地址值，而是内容，如果内容为空，则为`[]`
- `public boolean add(E e)`向集合中添加元素，参数的类型和泛型一致。返回值表示添加动作是否成功。对于`ArrayList`来说，`add`操作一定是成功的，对于其他集合不一定是成功的,
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
  1. `public String[] split(String regex)`按照参数规则将字符串切割成为若干部分，注意参数是一个正则表达式。

## 接口
- 接口就是多个类的公共规范，并且是一种引用类型，最重要的内容就是其中的抽象方法。如果是 Java 7 接口中可以包含的内容有
  1. 常量
  2. 抽象方法
  如果是 Java 8 可以包含的内容有
  3. 默认方法
  4. 静态方法
  如果是 Java 9 可以包含的内容有
  5. 私有方法
- 在任何版本的 Java 中间，接口都能定义抽象方法，接口中的抽象方法，必须是两个固定的关键字`public`和`abstract`，这两个关键字可以选择性地省略。
  `public abstract 返回值类型 方法名称（参数列表）`
- 接口使用步骤
  1. 接口不能直接使用，必须用一个实现类来实现接口
  2. 接口的实现类必须覆盖重写接口中的所有抽象方法
  3. 创建实现类的对象，进行使用
  > 如果实现类没有覆盖重写接口中的所有抽象方法，那么这个实现类自己就必须是抽象类
- 从 Java 8 开始，接口里允许定义默认方法，它可以解决接口升级问题
  `public default 返回值类型 方法名称（参数列表）{ 方法体 }`
- 从 Java 8 开始，接口中允许定义静态方法，不能通过接口实现类中的对象来调用接口中的静态方法。需要通过接口名称直接调用静态方法
  `public static 返回值类型 方法名称（参数列表）{ 方法体 }`
- 从 Java 9 开始，接口中允许定义私有方法
  1. 普通方法，为了解决多个默认方法之间重复代码问题
  `private 返回值类型 方法名称（参数列表）{ 方法体 }`
  2. 静态私有方法，为了解决多个默认静态方法之间的重复代码问题
  `private static 返回值类型 方法名称（参数列表）{ 方法体 }`
- 接口当中可以定义`成员变量`，但必须把`public`，`static`，`final`三个关键字进行修饰，当然也是可以省略。一旦赋值，不可修改。使用`final`就是表示不可一旦赋值，不可修改。
  `public static final 数据类型 常量名称 = 数据值`
- 常量推荐使用大写和下划线进行命名
- 当继承多个接口时，需要注意
  1. 接口是没有静态代码块或是构造方法的
  2. 如果存在重复的抽象方法，只需要重写或是覆盖一次即可
  3. 如果存在重复的默认方法，实现类就必须对重复的默认方法进行重写
  4. 如果父类的方法和接口中的方法产生了冲突，优先使用父类中的方法
- 接口继承接口时，如果默认方法重复，子接口需要重写，同时必须加上`default`关键字
- 接口可以作为成员变量类型，方法的参数或返回值

## 常见类
- `Objects`注意不是`Object`，它是一个工具类。当对象为 null 时候，调用`equals`方法会报错，所以可使用`Objects`的`equals`方法，会调用第一个参数的`equals`方法和第二个参数进行比较

### Collections
它是集合工具类，用于对集合进行操作，部分方法如下
- `public static <T> boolean addAll(Collections<T> c, T ... elements())`往集合中添加元素
- `public static void shuffle(List<?> list)`打乱集合顺序
- `public static <T> void sort(List<T> list)`将集合元素按默认顺序排序，但是注意被排序的集合里的元素必须实现`Comparable`，接口中的`compareTo`方法定义排序规则
- `public static <T> void sort(List<T> list, Comparator<? super T>)`将集合中元素按指定规则排序

## 常见接口
集合是 java 提供的一种容器，可以用来储存多种数据
集合按照其存储结构可以分为两大类，分别是单列集合`java.util.collection`和双列集合`java.util.map`

### Collection
单列集合类的根接口，它有两个重要的子接口，分别是`java.util.List`和`java.util.Set`。其中，`List`的特点是元素有序、元素可重复。`Set`的特点是元素无序，且不可重复。`List`接口的主要实现类有`java.util.ArrayList`和`java.util.LinkedList`，`Set`接口的主要实现类有`java.util.HashSet`和`java.util.TreeSet`
`Collection`就是所有单列集合中共用的方法
- `ArrayList`底层是数组实现的，查询快，增删慢
- `LinkedList`底层是链表实现的，查询慢，增删快
- `HashSet`底层是哈希表 + 红黑树实现的，无索引、不可以存储重复元素、存储无序
- `LinkedHashSet`底层是哈希表 + 链表实现的，无索引、不可以存储重复元素、可以保证存储顺序
- `TreeSet`底层是二叉树实现，一般用于排序
集合常用方法
- `boolean add(E, e)`向集合中添加元素
- `boolean remove(E, e)`删除集合中某个元素
- `void clear()`清空所有元素
- `boolean contains(E e)`判断集合中是否包含某个元素
- `boolean isEmpty()`判断集合是否为空
- `int size()`获取集合长度
- `Object[] toArray()`转换为数组，转换完成后可使用 for 循环遍历
- `Iterator<E> iterator()`返回在此 collection 元素上进行迭代的迭代器
常用功能

### List
`List`接口继承`Collection`接口，它是一个有序的集合，此接口用户可对列表中每个元素的插入位置进行精确地控制，允许重复元素。操作索引时，一定要防止索引越界异常
其实就是`ArrayList`，操作索引一定要防止索引越界异常
- `public void add(int index, E element)将指定元素添加到指定位置
- `public E get(int index)获取指定位置的元素
- `public E remove(int index)移除指定位置元素
- `public E set(int index, E element)用指定元素替换集合中指定位置元素，返回值的更新前元素

### LinkedList
`LinkedList`集合数据存储的是链表结构。方便元素添加删除的集合，它包含了大量操作首尾元素的方法。当使用`LinkedList`集合的特有方法时，不能使用多态
- `public void addFirst(E e)`将指定元素插入到列表的开头
- `public void addLast(E e)`将指定元素插入到列表的结尾
- `public E getLast()`获取最后一个元素
- `public E getFirst()`获取第一个元素
- `public E removeLast()`移除最后一个元素并返回
- `public E removeFirst()`移除第一个元素并返回
- `public E pop()`等同于`removeLast()`
- `public void push(E e)`将元素推入此列表所表示的堆栈
- `public boolean isEmpty()`等同于`addLast()`

### Vector
同步的集合，它的大小可以鞥剧需要增大或缩小，以适应创建`Vector`后进行添加或移除项的操作，使用比较少

### Set
`Set`接口继承自`Collection`接口，它不包含重复元素

### HashSet
此类继承`Set`接口
1. 它由一个哈希表(一个 HashMap 实例)支持，查询速度非常快
2. 它不保证`Set`的迭代顺序
3. 是一个无序的集合，存储元素和取出元素的顺序可能不一致
4. 没有索引，没有带索引的方法，不能使用简单的 for 循环遍历
哈希值是一个十进制的整数，由系统随机给出，其实就是对象的地址值，但是一个逻辑地址，不是数据实际存储的地址
- `int hashCode()`可以获取对象的哈希值，该方法底层调用的是本地操作系统的方法
- `String`重写了`Object`的`HashCode`，如果字符串相同则它们的`HashCode`值会相等
哈希表是`HashSet`集合存储数据的结构
- jdk1.8 之前，哈希表 = 数组 + 链表，jdk1.8 之后，哈希表 = 数组 + 链表 或是 哈希表 = 数组 + 红黑树(当集合长度大于 8) ，就是速度快
它存储数据到集合中，先计算元素的哈希值，如果哈希值相同则存储到同一个地方
当两个元素不同但是哈希值相同，就称为哈希冲突
`HashSet`如何避免元素重复
在调用`add`方法时，会先调用元素的`hashCode`方法比较，如果出现哈希冲突就会调用`equals`方法比较，判断元素是否重复。但如果 hash 值相同会存到同一地址
所以`HashSet`集合进行自定义元素存储时必须实现`hashCode`和`equals`方法

### LinkedHashSet
具有可预知迭代顺序的`Set`接口的哈希表和链接列表实现，底层是 哈希表 + 链表/红黑树 + 链表 多了一个链表用于记录元素存储顺序

### Iteration
`Iteration`用于迭代访问(遍历)`Collection`中的元素，因此它也被称为迭代器
迭代即在取元素之前判断集合中是否有元素，如果有，就把这个元素取出，继续进行判断，如此往复，直到把所有元素全部取出
获取迭代器的实现类对象，并且会把指针(索引)指向集合的`-1`索引，取出下一个元素后，会把指针后移一位
想要遍历`Collection`集合，那么就需要获取该集合迭代器来完成迭代操作
- `public Iteration iterator()`获取集合对应的迭代器，用来遍历元素
迭代器常用接口如下
- `public E next()`返回迭代的下一个元素
- `public boolean hasNext()`如果仍有元素可以迭代，返回 true
迭代器使用步骤
1. 使用集合中的`iteration`方法获取迭代器
2. 使用`hasNext`方法判断是否还有下一个元素
3. 会用`next`取出下一个元素，如果没有元素会抛出`NoSuchElementException`异常
```java
while(it.hasNext()) {
  it.next();
}
```
JDK1.5 之后有一个高级 for循环，专门用来遍历数组和集合。它的内部原理其实就是个`Iterator`迭代器，所以在遍历过程中，不能对集合进行增删操作
```java
for (元素的数据类型 变量：只能是 Collection集合 or 数组) { // do something }
```

### Map
`Map<K, V>`中`K`为此映射所维护的键类型，`V`为映射值的类型。它是将键映射到值的对象。一个映射不能包含重复的键；每个键最多只能映射到一个值
`Map`集合保证`key`是唯一的，作为`key`的元素，必须冲洗`hashCode`方法和`equals`方法，以保证`key`唯一
此接口完全取代`Dictionary`类，后者是一个抽象类
常用方法
- `public V put(K key, V value);`把指定的键和指定的值加入到`Map`集合中，`key`不重复的话，返回值是null，`key`重复的时候，会返回被替换的 value
- `public V remove(Object key);`把指定的键所对应的键值对元素在`Map`集合中删除，返回被删除元素的值，`key`不存在，返回 null，存在返回删除的 value
- `public V get(Object key);`根据指定的键，在 Map 集合中获取对应的值，`key`值操作如上
- `boolean containskey(Object key);`判断集合中是否包含指定的键
- `public Set<K> keySet();`获取 Map 集合中所有的键，存储到 Set 集合中，可通过这个方法来遍历`Set`之后找到对应键的值
- `public Set<Map.Entry<k, v>> entrySet()`获取到 Map 集合中所有的键值对对象的集合，返回映射的`collection`视图，只能通过此视图的迭代器来实现
在`Map`接口中有一个内部接口`Entry`，当`Map`集合一创建，那么就会在`Map`集合中创建一个`Entry`对象，用来记录键与值，它有两个方法
- `getKey()`获取键
- `getValue()`获取值

### HashMap
基于哈希表的`Map`接口的实现。此实现提供所有可选的映射操作，并允许使用`null`值和`null`键。它不保证映射的顺序
它的特性和`HashSet`一致

### LinkedHashMap
基于哈希表和链表实现，具有可预知的迭代顺序。
它的特性和`LinkedHashSet`一致

### HashTable
此类实现一个哈希表，此哈希表将键映射到相应的值。任何非 null 对象都可以作为键或值，但它是同步的，是一个线程安全的集合，速度慢
`HashTable`和`Vector`集合一样，在 jdk1.2 之后被更先进的`HashMap`集合取代了，但它的子类`Properties`依然活跃在历史舞台，它是唯一一个和`IO`流相结合的集合

### `of`方法
`List`，`Set`和`Map`接口都增加了一个静态方法`of`，可以给集合一次性添加多个元素。使用前提是当集合中存储的元素的个数已经确定了，不在改变时使用
注意
1. `of`方法只适用于`List`接口，`Set`接口，`Map`接口，不适用于接口的实现类
2. `of`方法的返回值是一个不能改变的集合，集合不能再使用`add`，`put`方法添加元素，会抛出异常
3. `Map`和`Set`接口调用`of`方法的时候，参数不能有重复的元素

## Lambda
标准格式
`(参数类型 参数名称) -> { 代码 }`
lambda 方法只能使用在只有一个抽象方法的接口，有且仅有一个抽象方法的接口称为函数式接口
可以省略的内容
1. (参数列表): 括号中参数列表的数据类型，可以省略不写
2. (参数列表): 括号中的参数只有一个，那么类型和`()`都可以省略
3. (一些代码): 如果加`{}`的代码只有一行，无论是否有返回值，都可以省略`{}, return, ;`，且必须一起省略

## 多线程
- 并发：指两个或多个事件在同一个时间段内发生
- 并行：指两个或多个事件在同一时刻发生（同时发生）
- 进程：指一个内存中运行的程序，每个进程都有一个独立的内存空间，一个应用程序可以同时运行多个进程；进程也是程序的一次运行过程，是系统运行程序的基本单位；程序运行一个程序就是一个进程从创建、运行到消亡的过程
- 线程：线程是进程的一个执行单元，负责当前进程中程序的执行，一个进程至少有一个线程
### 线程调度
- 分时调度：所有线程轮流使用 cpu 的使用权，平均分配每个线程占用 cpu 的时间
- 抢占式调度：优先让优先级高的线程使用 cpu，如果线程的优先级相同，就会随机选择一个，Java 使用的为抢占式调用
### 线程类
Java 使用`java.lang.Thread`类代表线程，所有的线程必须是`Thread`类或是其子类的实例，Java 中通过继承`Thread`类来创建并启动多线程的启动步骤如下
1. 定义`Thread`的子类，并重写该类的`run()`方法，该方法代表线程需要完成的任务
2. 创建`Thread`子类的实例，及创建了线程对象
3. 调用线程类的`start()`方法来启动线程，每次启动时会开辟新的栈空间，执行`run`方法
当 Java 执行了`main`方法，`main`方法就会进入栈内存，之后会创建一个主线程执行`main`方法
多次启动一个线程是非法的，特别是当线程已经执行后，不能再重新启动。启动线程后，所有线程会并行地执行
常用方法
- `public Thread()`分配一个新的线程对象
- `public Thread(String name)`分配一个指定名字的线程对象
- `public Thread(Runnable target)`分配一个带有指定目标新的线程对象
- `public Thread(Runnable target, String name)`分配一个带有指定目标指定名字的新的线程对象
常用方法
- `public String getName()`获取当前线程名称
- `public void start()`启动线程并执行
- `public void run()`定义线程要执行的任务
- `public static void sleep(long milis)`是当前执行的线程以毫秒数暂停
- `public static Thread currentThread()`返回当前正在执行的线程对象的引用
### Runnable 接口
我们只需要重写`run`方法即可，步骤如下
1. 定义`Runnable`接口的实现类，并重写该接口的`run()`方法
2. 创建`Runnable`接口的实例，并以此实例作为`Thread`的`target`来创建`Thread`对象
3. 调用该线程的`start`方法来启动线程
### 线程安全
如果有多个线程在同时运行，而这些线程可能会同时运行这段代码。程序每次运行的结果和单线程运行的结果是一样的。
- 同步代码块：`synchronized`关键字可以用于方法中的某个区块中，表示只对这个区块中的资源实行互斥访问，格式
```java
synchronized(同步锁) {
  需要同步操作的代码
}
```
- 同步锁：只是一个概念，可以想象为在对象上标记了一个锁
  1. 锁可以是任意类型
  2. 多个线程对象，使用同一个锁
- 同步技术原理：当使用了一个锁对象时，`t0`线程抢到了 cpu 的执行权，执行`run`方法，遇到`synchronized`代码块，这时`t0`会检查是否有锁对象，发现有就会获取到锁对象，进入到同步中执行。此时线程`t1`抢到了执行权，遇到`synchronized`代码块，如果没检查到锁对象，就会阻塞线程，直到`t0`线程归还锁对象
### 同步方法
使用了`synchronized`修饰的方法，就叫同步方法，保证当前线程在执行的时候，其他线程在方法外等着，使用步骤：
1. 把访问了共享数据的代码抽出来，放到一个方法中
2. 在方法上添加`synchronized`修饰符
这种方法的锁对象就是`this`
静态同步方法的锁对象是本类的`class`属性 --> `class`文件对象(反射)
### Lock 锁
`java.util.concurrent.locks.Lock`接口提供了比`synchronized`代码块和`synchronized`方法更广泛的锁定操作
`Lock`锁又称为同步锁，加锁与释放锁方法化了，如下:
- `public void lock();`加同步锁
- `public void unlock();`释放同步锁
使用步骤
- 在成员位置创建一个`ReentrantLock`对象
- 在可能出现安全问题的代码前调用`Lock`接口中的方法`lock`获取锁
- 在可能出现安全问题的代码前调用`Lock`接口中的方法`unlock`释放锁，可放在`finally`代码块中执行
### 线程状态
线程可处于下面几种状态之一
- `NEW`新建状态，至今尚未启动的线程
- `RUNNABLE`正在 Java 虚拟机中执行的线程处于这种状态
- `BLOCKED`阻塞状态，受阻塞并等待某个监视器锁的线程处于这种状态
- `WAITED`无限期等待另一个线程来执行某个特定操作的线程处于这种状态，等待 cpu 空闲时执行
- `TIMED_WAITED`休眠状态，等待另一个线程取决于等待时间的操作的线程，cpu 空闲时也不执行
- `TERMINATED`死亡状态，已退出线程，比如出现异常
`Timed Waiting(计时等待)`一个正在限时等待另一个线程执行一个动作的线程处于这种状态，也就是`sleep`，也可以使用`wait`方法传入毫秒值
`Object`类中的方法
- `void wait()`在其他线程调用此对象的`notify()`方法或是`notifyAll()`方法前，导致当前线程等待
- `void notify()`会唤醒在此对象监视器上等待的单个线程，会继续`wait()`之后的代码，如果有多个线程都在等待，随机唤醒一个
- `void notiryAll()`唤醒所有等待线程
### 线程通信
多个线程并发执行时，在默认情况下CPU是随机切换线程的，当我们需要多个线程共同完成同一任务，并希望它们有规律执行，那么多线程之间就需要一些协调通信
多个线程在处理同一个资源且任务不同时，需要线程通信来帮组解决线程之间对同一个变量的使用或操作，我们可以通过等待唤醒机制有效地利用资源
其实就是利用`wait`方法和`notify`方法，就是一个经典的生产者与消费者问题
### 线程池
如果并发的线程数量很多，并且每个线程都是指向一个很短的任务就结束了，这样频繁地创建线程就会大大地降低系统的效率。此时我们可以让线程得以复用，在执行完一个任务之后不被销毁，继续执行其他任务
线程池：容器为集合当程序第一次启动的时候，创建多个线程保存到集合中，当我们需要使用线程时，就从集合中取出线程来使用
`Thread t = list.remove(0)`
使用完毕时归还到线程池
`Thread t = list.add(t)`
在 JDK1.5 之后，JDK 内置了线程池，我们可以直接使用
`java.util.concurrent.Executors`：线程池的工厂类，用来生成线程池
`Executors`类中的静态方法：
`static ExecutorService newFixedThreadPool(int nThreads)`创建一个可重用固定线程数的线程池，参数为创建线程池中的线程数量，返回值为`ExecutorService`接口，返回的是它的实现类对象，可以使用`ExecutorService`接口接收
`java.util.concurrent.ExecurorService`线程池接口，用来从线程池中获取线程，调用`start`方法，执行线程任务
- `submit(Runnable task)`提交一个`Runnable`任务用于执行
- `void shutdown()`关闭/销毁线程池方法
线程池使用步骤
1. 使用线程池工厂类`Executors`里提供的静态方法`newFieldThreadPool`生产一个指定线程数量的线程池
2. 创建一个类，实现`Runnable`方法，重写`run`方法，设置线程任务
3. 调用`ExecutorService`中的方法`submit`传递线程任务(实现类)，开启线程，执行`run`方法
4. 调用`ExecutorService`中的方法`shutdown`销毁线程池，但不建议执行

## File 类
`java.io.File`类，为文件和目录路径的抽象表现形式，把文件和文件夹封装成了一个`File`类
- `static String pathSeparator`：与系统有关的路径分隔符，为了方便，它被表现为一个字符串。windows：分号，linux：冒号
- `static char pathSeparator`：与系统有关的路径分隔符
- `static String separator`：与系统有关的默认名称分隔符。windows：反斜杠，linux：正斜杠
- `static char separator`：与系统有关的默认名称分隔符
所以操作路径的时候不要写死了，应该使用上面的分隔符
### 构造方法
- `File(String path)`：路径可以是以文件或是以文件夹结尾，可以是相对路径也可以是绝对路径，路径可以存在也可以不存在
- `File(String parent, String child)`：根据`parent`路径名字符串和`child`路径名字符串创建一个新的`File`实例，父路径是`File`类型，可以使`File`的方法对路径进行一些操作，再使用路径创建对象

### 常用方法
- `public String getAbsolutePath()`：返回此`File`的绝对路径
- `public String getPath()`：将此`File`转换为路径名字符串
- `public String getName()`：返回此`File`表示的文件或目录名称，也就是结尾
- `public long length()`：返回此`File`表示的文件的长度，文件夹则返回`0`
- `public boolean exist()`：此`File`表示的文件或目录是否实际存在
- `public boolean isDirectory()`：此`File`表示的是否为目录
- `public boolean isFile()`：此`File`表示的是否为文件
- `public boolean createNewFile()`：当且仅当具有该名称的文件尚不存在时，创建一个空文件，当路径不存在时，必须处理`IOException`异常
- `public boolean delete()`：删除此`File`表示的文件或目录，当文件夹中有内容，会返回`false`，删除是直接在硬盘删除
- `public boolean mkdir()`：创建此`File`表示的目录，创建单级文件夹
- `public boolean mkdirs()`：创建此`File`表示的目录，包括任何必需单不存在的目录，可创建单级或是多级文件夹
- `public String[] list()`：返回一个String数组，表示该`File`目录中所有的子目录和文件，遍历的是构造方法给出的目录，如果路径不存在，会抛出空指针异常，获取的是目录名或是文件名
- `public File[] listFiles()`：返回一个File数组，表示该`File`目录中所有的子目录和文件

### 过滤器
- `java.io.FileFilter`：此接口的实例用于抽象路径名的过滤器，此接口实例可传递给`File`类的`listFiles(FileFilter)`方法
  - `boolean accept(File pathname)`：测试指定抽象路径名是否应该包含在某个路径名列表中
- `java.io.FilenameFilter(File pathname)`：此接口的实例用于文件名的过滤
  - `boolean accept(File dir, String name)`：此时指定文件是否应该包含在某一文件列表中

## IO
- `i(input)`：输入（读取）
- `o(output)`：输出（写入）
- `流`：数字，字符，字节，一个字符等于`2`个字节，一个字节等于`8`个二进制位
  - `inputStream`：字节输入流
  - `outputStream`：字节输出流
  - `Reader`：字符输入流
  - `Writer`：字符输出流
- `java.io.outputStream`：此抽象类是表示字节输出类的所有类的超类，它定义了字符输出流的基本共性功能方法
  - `public void close()`：关闭此输出流并释放与此流相关联的任何系统资源
  - `public void flush()`：刷新此输出流并强制任何缓冲的输出字节被写出
  - `public void write(byte[] b)`：将`b.length`字节从指定的字节数组写入此输出流
  - `public void write(byte[] b, int off, int len)`：从指定的字节数组写入`len`字节，从偏移量`off`开始输出到此输出流
  - `public abstract void write(int b)`：将指定的字节输出流
- `java.io.FileOutputStream`：文件字节输出流，把内存中的数据写入到硬盘的文件中
  - `FileOutputStream(String name)`：创建一个向具有指定名称的文件中写入数据的输出文件流，目的是一个文件路径
  - `FileOutputStream(File file)`：创建一个指定`File`对象表示的文件中写入数据的文件输出流，目的是一个文件
  构造方法的作用
  1. 创建一个`FileOutputStream`对象
  2. 根据指定的文件路径或是文件创建一个空文件
  3. 把`FileOutputStream`对象指向创建好的文件
  写入数据的原理(内存 -> 硬盘)：java程序 --> JVM虚拟机--> 操作系统--> 把数据写到文件中
  字节输出流的使用步骤
  1. 创建一个`FileOutputStream`对象，构造方法中传递写入数据目的地
  2. 调用`FileOutputStream`对象中的方法`write`，把数据写入到文件中
  3. 释放资源(流会占用一定的资源，使用完需要清空内存，提高程序的效率)
  ```java
  FileOutputStream fos = new FileOutputStream('路径');
  // 写数据的时候，会把十进制数97转为二进制整数97，如果通过记事本打开会查询编码表，把字节转换为字符表示
  fos.write(97);
  fos.close();
  ```
  如果写的第一个字节是正数`0 ~ 127`，那么显示的时候会查询`ASCII`表
  如果写的第一个字节是负数，那么第一个字节会和第二个字节，两个字节组成一个中文显示，查询系统默认码表`GBK`
  - `String.getBytes()`：可以将字符串转换为字节数组
  - `public void write(byte[] b)`：将`b.length`字节从指定的字节数组写入输出表，这个和下个方法都是为了一次性写入多个字节
  - `public void write(byte[] b, )`：从指定的字节数组写入`len`字节，从偏移量`off`开始输出到此输出流
  - `FileOutputStream(String name, boolean append)`：在指定文件后进行追加写入，当`append`为`false`时，覆盖原文件，`true`时，追加写入
  - `FileOutputStream(File file, boolean append)`：在指定文件后进行追加写入
  换行符：写换行符号
  - `windows`：`\r\n`
  - `linux`：`\n`
  - `mac`：`\r`
- `java.io.InputStream`：表示字节输入流的所有类的超类，可以读取字节信息内存中
  - `public void close()`：关闭此输入流并释放与此流相关联的任何系统资源
  - `public abstract int read()`：从输入流读取数据的下一个字节，可以使用`while`循环进行读取
  - `public int read(byte[] b)`：从输入流中读取一定数量的字节并将其存储到缓冲区数组`b`中
  ```java
  while((len = fis.read()!=-1) {
    system.print((char)len;)
  }
  ```
  - `public int read(byte[] b)`：从输入流中读取一些字节数，并将它们存储到字节数组`b`中，读取到文件末尾返回`-1`
  ```java
  byte[] bytes = new byte[2];
  int len = fis.read(bytes);
  // String 可将读取的数据流转换为字符串
  System.out.println(new String(bytes));

  // 读取有效字节
  int len = 0
  while(len! = -1) {
    System.out.println(String(bytes, 0, len)); 
  }
  ```
- `java.io.FileInputStream`：把硬盘中的文件，读取到内存中使用
  - `FileInputStream(Stream name)`：文件路径
  - `FileInputStream(File file)`：文件
  字节输入流的使用步骤
  1. 创建`FileInputStream`对象，构造方法中绑定要读取的数据源
  2. 使用`FileInputStream`对象中的`read`方法，读取文件
  3. 释放资源
  - `String(byte[] bytes)`：可以把字节数组转换为字符串
  - `String(byte[] bytes, int offset, int length)`：可以把字节数组的一部分转换为字符串

## 字符流
当字节流读取文本时，可能会有一些小问题，就是遇到中文字符时，可能不会显示完整的字符，那是因为一个中文字符可能会占用多个字节存储，所以 java 会提供字符流来专门处理文本文件
- `java.io.Reader`：此抽象类专门用于读取字符流所有类的超类，可以读取字符信息到内存中，它定义了一些共性方法
  - `public void close()`：关闭并释放与此流相关的系统资源
  - `public int read()`：从输入流读取一个字符
  - `public int read(char[] cbuf)`：从输入流中读取一些字符，并将它们存储到数组`cbuf`中
使用字节流读取中文文件，1个中文
  - `GBK`：占用两个字节
  - `UTF-8`：占用三个字节
- `java.io.FileReader extends InputStreamWriter extends Reader`：文件字符输入流，把硬盘文件中的数据以字符的方式读取到内存中，创建一个`FileReader`对象，将它指向要读取的文件
  - `FileReader(String fileName)`：接收一个路径
  - `FileReader(File file)`：接受一个文件
  - 和字节流类似，使用`String(char[] value)`和`String(char[] value, int offset, int count)`来转化为字符串
- `java.io.FileWriter extends OutputStreamWriter extends Reader`：此抽象类是表示写出字符流的所有类的超类，将指定的字符写在目的地
  - `void write(int c)`：写入单个字符
  - `void write(char[] cbuf)`：写入字符数组
  - `abstract void write(char[] cbuf, int off, int len)`：写入字符数组的某个部分
  - `void write(String str)`：写入字符数组
  - `void write(String str, int off, int len)`：写入字符串的某一部分
  - `void flush()`：刷新该流的缓冲，流对象和以继续使用
  - `void close()`：关闭此流，但要先刷新它。先刷新缓冲区，然后通知系统释放资源，流对象不可以再被使用了
  字符输出流的使用步骤
  1. 使用`FileWriter`对象，构造方法中绑定要写入数据的目的地
  2. 使用`FileWriter`中的方法`write`，把数据写入到内存缓冲区中(字符转为字节过程)
  3. 使用`FileWriter`中的方法`flush`，把内存缓冲区中的数据，刷新到文件中
  4. 释放资源
  - `FileWriter(String fileName, boolean append)`：与字节流类似，`true`续写，`false`覆盖
  - `FileWriter(File file, boolean append)`：与字节流类似

## IO 异常的处理
在 JDK1.7 之前使用`try...catch...finally`处理流中的异常，`finally`中释放资源
```java
FileWriter fw = null
try {
  fw = new FileWriter("路径名");
  for (int i = 0; i < 10; i++) {
    fw.writer("hello");
  }
} catch(IOException e) {
  System.out.println(e);
} finally {
  if (fw != null) {
    try {
      fw.close();
    } catch(IOException e) {
      e.printStackTrace();
    }
  }
}
```
JDK 7的新特性，在`try`的后边可以增加一个括号`()`，在括号中可以定义流对象，那么这个流对象的作用域在`try`中有效，`try`中代码执行完毕，会自动把流对象释放，不用写`finally`
```java
try (
  FileInputStream fis = new FileInputStream('c:jpg');
  FileOutputStream fos = new FileOutputStream('c:jpg');
) {
  int len = 0;
  while((len = fis.read()) != -1) {
    fos.write(fos);
  }
} catch(IOException e) {
  System.out.println(e);
}
```
JDK 9 的新特性，可以在`try`之前定义流对象，之后在`try`中引用。但是会抛出`IOException`错误

## Properties
`Properties`类表示了一个持久地属性集。`Properties`可保存在流中或是从流中加载。属性列表中每个键及其对应值都是字符串
- `java.util.properties extends HashTable<k, v> implements Map<k, v>`，它是唯一一个和 IO流结合的集合
  - `void store(OutputStream out, String comments)`：字节输出流，不能写入中文，`comments`用于表示保存的文件是做什么用的，不能使用中文，默认是`unicode`编码，一般使用空字符串。此方法可以把集合中的临时数据持久化写入到硬盘中存储
  - `void store(Writer writer, String comments)`：字符输出流，可以写入中文，此方法可以把集合中的临时数据持久化写入到硬盘中存储
  使用步骤
    1. 创建`Properties`集合对象，添加数据
    2. 创建字节输出流/字符输出流对象，构造方法中绑定要输出的目的地
    3. 使用`Properties`集合中的方法`store`
    4. 释放资源
  - `void load(InputStream inStream)`：可以把硬盘中保存的文件(键值对)读取到集合中使用，不能读取含有中文的键值对
  - `void load(Reader reader)`：可以把硬盘中保存的文件(键值对)读取到集合中使用，可以读取中文
  使用步骤
    1. 创建`Properties`集合对象
    2. 使用`Properties`集合对象中的方法`load`读取保存键值对的文件
    3. 遍历`Properties`集合
  注意
    - 存储键值对的文件中，键值对默认的连接符号可以为`=`，`<space>`或是其他符号
    - 可以使用`#`进行注释
    - 键与值默认都是字符串
  由于它的键和值都是字符串，所以它会有一些操作字符串的特有方法
  - `Object setProperty(String key, String value)`：调用`Hashtable`的方法`put`
  - `String setProperty(String key)`：用指定的键在此属性列表中搜索属性，此方法相当于`Map`集合中的`get(key)`方法
  - `Set<String> stringPropertyNames()`：返回此属性列表中的键集，其中该键及其对应值是字符串，此方法相当于`Map`集合中的`keySet`方法

## 缓冲流
是在基本的流对象的基础之上创建而来的，能够转换编码的转换流，能够持久化存储对象的序列化流等等。如果不使用缓冲流，那么每次都是单个字节进行写入和读取都要经过 JVM、OS、硬盘这几个步骤来回传递数据，十分消耗资源。而字节缓冲输入流能给基本的字节输入流增加一个缓冲区(数组)，提高基本字节输入流的读取效率

### 字节缓冲流
- `java.io.BufferedOutputStream extends OutputStream`：可以使用`OutputStream`的基本方法
  - `BufferedOutputStream(OutputStream out)`：创建一个新的缓冲输出流，以将数据写入指定的底层输入流
  - `BufferedOutputStream(OutputStream out, int size)`：创建一个新的缓冲输出流，以将具有指定缓冲区大小的数据写入指定的底层输出，`size`用于限定大小，不传默认为默认值
  使用步骤
  1. 创建`FileOutputStream`对象，构造方法中绑定要输出的目的地
  2. 创建`BufferedOutputStream`对象，构造方法中传递`FileOutputStream`对象，提高`FileOutputStream`对象效率
  3. 创建`BufferedOutputStream`对象中的方法`write`，把数据写入到内部缓冲区中
  4. 创建`BufferedOutputStream`对象中的方法`flush`，把内部缓冲区的数据刷新到文件中 
  5. 释放资源，会调用第4步的方法刷新资源，所以第四步可以省略
- `java.io.BufferedInputStream extends InputStream`：可以使用`InputStream`的基本方法
  - `BufferedInputStream(InputStream in)`：创建一个新的缓冲输入流，以将数据写入指定的底层输入流
  - `BufferedInputStream(InputStream in, int size)`：创建一个新的缓冲输入流，以将具有指定缓冲区大小的数据写入指定的底层输出，`size`用于限定大小，不传默认为默认值
  使用步骤
  1. 创建`FileInputStream`对象，构造方法中绑定要读取的数据源
  2. 创建`BufferedInputStream`对象，构造方法中传递`FileInputStream`对象，提高`FileInputStream`对象效率
  3. 使用`BufferedInputStream`对象中的方法`read`，读取文件
  4. 读取资源
- `java.io.BufferedWriter`和`java.io.BufferedReader`和字节流的用法基本相同，只不过可以直接处理中文，它可以使用`缓冲流.newLine()`来进行换行
  - `String readLine()`：读取一个文本行。读取一行数据行的终止符号也就是`\n`或是`\r`或是`\r\n`直接跟着换行，返回该行的字符串不包含终止符号。如果结束则返回`null`可以用它来作为`while`的终止条件`

## 字节编码和字符集
- 计算机中存储的信息是用二进制来表示的，我们在屏幕上看到的数字、英文、标点符号、汉字等字符是二进制数是转换后的结果。按照某种规则，将字符保存在计算机中，称为编码。反之，将二进制数按某种规则显示出来称为解码。
  - 编码：字符 -> 字节
  - 解码：字节 -> 字符
  - 字符编码`charater Encoding`：就是一套自然语言的字符与二进制数之间的对应规则
  - 编码表：对应规则，是一个系统支持的所有字符的集合，常见的有`ASCII`、`GBK`、`unicode(utf-8)`等
### 字符集
- `ASCII`：是基于拉丁字母的一套电脑编码系统，用于显示现代英语。基本的`ASCII`字符集，使用`7bits`表示一个字符，共128字符，扩展字符集使用`8bits`表示一个字符，共256字符
- `ISO-8859-1`：拉丁码表，用于显示欧洲使用的语言，使用单字节编码，兼容`ASCII`
- `GBxxx`：`GB`就是国标的意思，为了显示中文而设计的一套字符集
  - `GB2312`：简体中文码表，一个小于127的字符和原来相同。但两个大于127的字符连在一起时就表示一个汉字，两个字节长的字符叫`全角`字符，而一个字节长的字符叫`半角`字符
  - `GBK`：最常用的中文码表，是在`GB2312`基础上扩展而来，使用了双字节编码方案，共收录了21003个汉字，完全支持`GB2312`，同时兼容繁体汉字以及日韩汉字
  - `GB18030`：最新的中文码表，收录汉字70244个，采用多字节编码，每个字符可以由1、2或是4个字节组成
- `unicode`：为表达任意语言的任意字符设计的，是业界的统一标准，也称为统一码，万国码。它最多使用4个字节的数字来表达每个字母、符号或文字。有三种编码方案，`utf-8`、`utf-16`、`utf-32`。最常用的为`utf-8`
  - `utf-8`：互联网工作小组要求所有互联网协议都必须支持`UTF-8`编码，编码规则:
    - 128个`US-ASCII`字符，只需要一个字节编码
    - 拉丁文等字符，需要两个
    - 大部分常用字(含中文)，使用三个字节
    - 其他极少用的Unicode辅助字符，使用四字节编码
 
- 在IDEA中，使用`FileReader`读取项目中的文件，由于默认的是`utf-8`编码，但由于 windows 中的编码为`GBK`，所以会出现乱码

### 转换流
- `InputStreamReader`：`Reader`的子集，它读取字节并使用指定的字符集将其解码为字符。它的字符集可以由名称指定，也可接受默认字符集
  - `InputStreamReader(InputStream in)`：创建一个使用默认字符集的字符流
  - `InputStreamReader(InputStream in, String charsetName)`：创建一个使用指定字符集的字符流
- `OutputStreamWriter`：`Writer`的子集，具体用法和`InputStreamReader`类似
  - 使用步骤
    1. 创建`OutputStreamWriter`对象，构造方法中传递字节输出流和指定的编码表名称
    2. 使用`OutputStreamWriter`中的方法`write`，把字符转换为字节存储在缓冲区中
    3. 使用`OutputStreamWriter`中的方法`flush`，把字节缓冲区的字节刷新到文件中
  ```java
  OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream('文件路径'));
  osw.writer("你好");
  osw.flush();
  osw.close();
  ```
### 序列化
java 提供了一种对象序列化的机制。用一个字节序列可以表示一个对象，该字节序列包含该`对象的数据`、`对象的类型`、`对象中存储的属性`等信息。字节序列存储到文件中时，相当于文件中持久保存了一个文件的信息。
把文件中保存的对象，以流的方式读取出来，叫做对象的反序列化
- `java.io.ObjectOutputStream extends OutputStream`类，将java中的原始数据类型写出到文件中，实现对象的持久存储
  - `public ObjectOutputStream(OutputStream out)`：创建一个指定`OutputStream`的`ObjectOutputStream`
  - `void writeObject(Object obj)`：将指定的对象写入`ObjectOutputStream`
  使用步骤
  1. 创建`ObjectOutputStream`对象，构造方法中传递字节输出流
  2. 使用`ObjectOutputStream`中的`writeObject`方法，把对象写入文件
  3. 释放资源
- `java.io.Serializable`：类通过实现此接口以实现其序列化功能，未实现此接口的类将无法使其任何状态序列化或反序列化。可序列化类的所有子类都是可序列化的。此接口没有字段和方法，仅用于表示可序列化的语义
  - 此接口也叫标记型接口，实现此接口就会给类添加一个标记，当进行序列化或是反序列化时，就会检测类上是否有这个标记，没有就会抛出`NotSerializableException`异常
- `java.io.ObjectInputStream extends InputStream`类，将序列化的原始数据恢复为对象
  - `ObjectInputStream(InputStream in)`：创建一个指定`InputStream`的`ObjectInputStream`
  - `Object readObject()`：读取保存对象的文件
  使用步骤
  1. 创建`ObjectInputStream`对象，构造方法中传递字节输入流
  2. 使用`ObjectInputStream`中的`readObject`方法，读取保存对象的文件
  3. 释放资源
  4. 使用对象
  当不存在对象的`class`文件时，抛出`ClassNotFoundException`异常
  反序列化的前提
  - 类必须实现`Serializable`
  - 必须实现类对象的 class 文件
- `static`关键字，静态关键字。静态优于对象进入到内存中。被`static`修饰的成员变量是不能被序列化的，序列化的都是对象
- `transient`关键字，瞬态关键字。被它修饰的成员变量，不能被序列化
- 当 JVM 在序列化对象时，能找到class文件，但是class文件在序列化对象之后发生了修改，那么反序列化操作也会失败，抛出一个`InvalidClassException`异常。异常原因如下
  - 该类的序列版本号和流中读取的类描述的版本号不匹配
  - 该类包含未知数据类型
  - 该类没有可访问的无参数构造方法
- 编译器会把java文件编译生成class文件，如果这个类实现了`Serializable`接口，就会给`class`文件生成一个序列号。当修改类文件时，序列号也会重新生成。如果想要不重新生成序列号，可以在类中添加一个成员变量`static final long serialVersion = 42L`

## XML
概念：`Extensible Markup Language(可扩展标记语言)`，可扩展，标签都是自定义的，功能
1. 配置文件
2. 在网络中传输
基本语法：
1. `.xml`：后缀名
2. `<?xml version='1.0' ?>`：第一行必须为版本设置
3. `xml`中有且仅有一个根标签
4. 属性值必须使用引号引起来
5. 标签必须正确关闭
6. 标签名称区分大小写
组成部分：
1. 文档声明，属性列表：
  - `version`：版本号，必须的属性
  - `encoding`：编码方式。告知解析引擎当前文档使用的字符集，默认值：`ISO-8859-1`
  - `standalone`：是否独立，值为`yes(不依赖其他文件)`或`no(依赖其他文件)`
2. 指令：结合`css`来控制标签样式`<?xml-stylesheet type="text/css" href="a.css" ?>`
3. 标签规则：
  - 名称可以包含字母、数字以及其他字符
  - 名称不能以数字或标点符号开始
  - 名称不能以字母`xml | XML`开始
  - 名称不能包含空格
4. 属性：`id`属性值唯一
5. 文本：
  - `CDATA区`：文本会被原样展示`<![CDATA[ <文本> ]]>`

### XML 约束
其实就是规定`xml`文档的书写规则，由软件提供，我们仅仅是阅读文档就行
分类：
1. `DTD`：简单，引入`dtd`文档到`xml`文档中，两种
  - 内部`dtd`：将约束规则定义在`xml`文档中
  - 外部`dtd`：将约束规则定义在外部的`dtd`文件中，本地`<!DOCTYPE <根标签> SYSTEM "<dtd 文件位置>">`，网络`<!DOCTYPE <根标签> PUBLIC "<dtd文件名>" "<dtd文件位置URL>" >`
2. `Schema`：复杂，其实就是一个`xml`文档，引入
  - 填写`xml`文档的根元素
  - 引入`xsi`前缀，`xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"`，约束文档标准
  - 引入`xsd`文件命名空间，`xsi:schemaLocation="http://www.itcast.cn/xml student.xsd"`，引入的路径
  - 为每一个`xsd`约束声明一个前缀，作为标识`xmlns:a="http://www.itcast.cn/xml"`，`:a`为给命名空间设置一个别名，如果只有一个引入，则可以不配置

### XML 解析
将文档中的数据读取到内存中，方式有
1. `DOM`：将标记语言一次性加载进内存中，在内存中形成一棵`dom`树
  - 优点：操作方便，可对文档进行`CRUD`的所有操作，在服务器中一般是这种
  - 缺点：树形结构占内存
2. `SAX`：逐行读取，基于事件驱动，读下一行时会将上一行释放，相当于在内存中只有一行，在移动端一般是这种
  - 优点：不占内存，手机中一般是这个
  - 缺点：只能读取，不能增删改
常见的解析器
- `JAXP`：官方提供，支持`dom`和`sax`，一般没人用
- `DOM4J`：非常优秀的解析器
- `PULL`：安卓内置解析器，`sax`方式的
- `jsoup`：`html`解析器，也可用于解析`xml`，使用步骤
  1. 导入`jar`包
  2. 获取`Document`对象
  3. 获取对应标签`Element`对象
  4. 获取数据
  常见对象
  - `Jsoup`：工具类，可以解析`html`或`xml`文档，返回`Document`
    - `parse`：解析`html`或`xml`文档，返回`Document`
      - `parse(File in, String charsetName)`：解析`xml`或`html`问阿金
      - `parse(String html)`：解析`xml`或`html`字符串
      - `parse(URL url, int timeoutMillis)`：通过网络路径获取指定的`html`或`xml`的文档对象，第二个参数为超时时间
  - `Document`：文档对象，代表内存中的`dom`树，主要用于获取`Element`对象
  - `Elements`：元素`Element`对象的集合，可以当做`ArrayList<Element>`来使用
  - `ELement`：元素对象，有以下功能
    - 获取子元素对象
    - `String attr(String key)`：获取属性值
    - `String test()`：获取文本内容
    - `String html()`：获取`innerHTML`
  - `Node`：节点对象

  快捷查询方式
  - `selector`：选择器，返回`Elements`
    - `select(String cssQuery)`：可以参考`Selector`类中定义的语法
  - `xpath`：`XML`路径语言，是一种用来确定`XML`文档中某部分位置的语言，使用`jsoup`的`XPath`需要导入额外的`jar`包
    - `JXDocument jxDocument = new JXDocument(document);`：根据`docuemnt`对象创建`JXDocument`对象
    - `List<JXNode> jxNodes = jxDocument.selN("<xpath>")`：结合`xpath`语法查询
