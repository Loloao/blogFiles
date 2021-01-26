## 接口、lambda 表达式与内部类

接口`interface`用来描述类应该做什么，而不指定它们具体应该如何做。一个类可以实现多个接口。有些情况可能要求符合这些接口，只要有这种要求，就可以使用实现了这个接口的类（即实现类）的对象。`lambda`表达式可以用来创建在将来某个时间点执行的代码块。

内部类定义在另外一个类的内部，它们的方法可以访问包含它们的外部类的字段。它在设计具有相互协作关系的类集合时很有用

### 接口

接口不是类，而是对希望符合这个接口的类的一组需求

接口中的所有方法都自动是`public`方法。在接口中声明方法时，不必提供关键字`public`。接口还可以定义常量。接口不会有实例字段，接口没有实例。提供实例字段和方法实现的任务应该由实现接口的那个类来完成。因此，可以将接口看成是没有实例字段的抽象类。但这两个概念还是有一定的区别。

为了让类实现一个接口，通常需要完成下面两个步骤：

1. 将类声明为实现给定的接口
2. 对接口中的所有方法提供定义

要将类实现为某个接口，需要使用关键字`implements`：`class Employee implements Comparable`

```java
public interface Comparable {
    int compareTo(Object other);
}

// 此时，类的实现需要提供 compareTo 方法
public int compareTo(Object otherObject) {
	Employee other = (Employee) otherObject;
    return Double.compare(salary, other.salary);
}

// 我们还可以为泛型 Comparable 接口提供一个类型参数
public int compareTo(Employee otherObject) {
    return Double.compare(salary, other.salary);
}
```

> `compare`方法将返回一个整数。如果两个对象不相等，则返回一个正值或是一个负值。在对两个整数字段进行比较时，这种灵活性非常有用。但是这里的相减技巧并不适用于浮点值

我们已经看到，要让一个类使用排序服务必须让它实现`compareTo`方法，此时我们就可以使用接口来让这个类必须拥有`compareTo`方法

P225 对一个`Employee`类实例数组进行排序

*接口的属性*

接口不是类，具体来说，不能使用`new`运算符实例化一个接口：`x = new Comparable( ... ); // ERROR`

尽管不能构造接口的对象，但可以声明接口的变量：`Comparable x; // OK`

接口变量必须引用实现了这个接口的类对象：`x = Employee( ... ); // OK provided Employee implements Comparable`

接下来，如同使用`instanceof`检查一个对象是否属于某个特点类一样，也可以使用`instanceof`检查一个对象是否实现了某个特定接口：`if (anObject instanceof Comparable) { ... }`

与建立类的继承层次一样，也可以扩展接口。允许有多条接口链，从通用性较高的接口扩展到专用性较高的接口，比如，假设一个名为`Moveable`的接口：

```java
public interface Moveable {
    void move(double x, double y);
}

// 然后，可以假设一个名为 Powered 的接口扩展了以上 Moveable 接口
public interface Powered extends Moveable {
    double milesPerGallon();
}
```

虽然接口中不能包含实力字段，但是可以包含常量。例如：

```java
public interface Powered extends Moveable {
	double milesPerGallon();
    double SPEED_LIMIT = 95;
}
```

与接口中的方法都自动被设置为`public`一样，接口中的字段总是`public static final`

尽管每个类只能有一个超类，但却可以实现多个接口，这就为定义类的行为提供了极大地灵活性，可以使用逗号将想要实现的各个接口分隔开

`class Employee implements Cloneable, Comparable`

*接口与抽象类*

抽象类与接口用法就是因为 Java 中不支持多重继承，但是可以使用多个类。

*静态和私有方法*

允许在接口中增加静态方法，但这有违于将接口作为抽象规范的初衷。在 Java 9 中，接口中的方法可以使`private`。`private`方法可以是静态方法或是实例方法。

*默认方法*

可以为接口方法提供一个默认实现。必须用`default`修饰符标记这样一个方法

```java
public interface Comparable<T> {
    default int compareTo(T other) { return 0; }
}
```

不过，`Comparable`的每一个实现都会覆盖这个方法。

*解决默认方法冲突*

如果先在一个接口中将一个方法定义为默认方法，然后又在超类或另一个接口中定义同样的方法，则会发生什么情况？

Java 的相应规则要简单的多。规则如下：

1. 超类优先，同名而且有相同参数类型的方法会被忽略

2. 接口冲突，如果一个接口提供了一个默认方法，另一个接口提供了一个同名且参数类型相同的方法，必须覆盖这个方法解决冲突

   ```java
   interface Person {
       default String getName() { return ""; };
   }
   
   interface Named {
       default String getName() { return getClass().getName() + "_" + hashCode(); }
   }
   
   // 如果继承两个接口会报冲突，即使其中一个接口的方法不是默认的，程序员必须解决这个二义性
   class Student implements Person, Named {
       public String getName() { return Person.super.getName(); }
   }
   ```

*接口与回调*

回调是一种常见的程序设计模式。在这种模式中，可以指定某个特定时间发生时应该采取的动作。

在`java.swing`包中有一个`Timer`类，如果希望经过一段时间间隔就得到通知，`Timer`类就很有用。构造定时器时候，可以向定时器传入某个类的对象，然后定时器调用这个对象的方法，由于对象可以携带一些附加信息，所以传递一个对象比传递一个函数要灵活的多

当然定时器需要知道调用哪个方法，并要求传递的对象所属的类实现了`java.awt.event`包的`ActionListener`接口

```java
public interface ActionListener {
    void actionPerformed(ActionEvent event);
}

class TimePrinter implements ActionListener {
    public void actionPerformed(ActionEvent event) {
        System.out.printf("At the tone")
    }
}

var listener = new TimerPrinter();
Timer t = new Timer(1000, listener);
```



*Comparator接口*

我们已经了解了如何对一个对象数组进行排序，前提是这些对象实现了`Comparable`接口的类的实例。`String.compareTo`方法可以按字典顺序比较字符串，单现在我希望按长度递增的顺序对字符串进行排序。要处理这种情况，`Arrays.sort`还有第二个版本，有一个数组和比较器作为参数，比较器是实现了`Comparator`接口的类的实例

```java
public interface Comparator<T> {
    int compare(T first, T second);
}

class LengthComparator implements Comparator<String> {
	public int compare(String first, String second) {
        return first.length() - second.length()
    }
}

var comp = new LengthComparator();
// 这个 compare 是在比较器方法上调用而不是在字符串本身上调用
if (comp.compare(words[i], words[j]) > 0) ...
```

*对象克隆*

`Cloneable`接口指示一个类提供了一个安全的`clone`方法，有关的技术细节性很强，现在只是稍微了解

使用`clone`方法可以创建一个有不同状态的新对象

```java
Employee copy = original.clone();
copy.raiseSalary(10);
```

不过并没有这么简单，`clone`方法只是`Object`的一个`protected`方法，这说明你的代码不能直接调用这个方法，只有`Employee`类可以克隆`Employee`对象。但是这里我们得考虑子对象是否克隆的问题

对于每一个类，我们需要确定：

1. 默认的`clone`方法是否满足要求；
2. 是否可以在可变的子对象上调用`clone`来修补默认的`clone`方法；
3. 是否不该使用`clone`；

实际上第三个选项是默认选项。如果西安则第一或第二项，类必须：

1. 实现`Cloneable`接口
2. 重新定义`clone`方法，并制定`public`访问修饰符

`Cloneable`接口的出现与接口的正常使用并没有关系。它没有指定`clone`方法，这个方法是从`Object`类继承的。这个接口只是作为一个标记，指示类设计者了解克隆过程。对象对于克隆很偏执，如果一个对象请求克隆，但是没有实现整个接口，就会生成一个检查型异常。

```java
class Employee implements Cloneable {
	public Employee clone() throws CloneNotSupportedException {
        Employee cloned = (Employee) super.clone();
        cloned.hireDay = (Date) hireDay.clone();
        return cloned;
    }
}
```

> 所有数组类型都有一个公共的 clone 方法，而不是受保护的。可以用这个方法建立一个新数组，包含原数组所有元素的副本

### lambda 表达式

`lambda`表达式是一个可传递的代码块，可以在以后执行一次或多次。

*lambda 表达式的语法*

我们传入代码来检查一个字符串是否比另一个字符串短，这里要计算：

`first.length() - second.length()`

我们此时还需要指定`first`和`second`的类型

```java
// 下面就是一个 lambda 表达式，就是一个代码块，以及必须传入代码的变量规范
(String first, String second)
	-> first.length() - second.length()
```

如果代码要完成的计算无法放在一个表达式中，就可以像写方法一样，把这些代码放在`{}`中，并包含显式的`return`语句。例如：

```java
(String first, String second) -> {
    if (first.length() < second.length()) return -1;
    else if (first.length() > second.length()) return 1;
    else return 0;
}
```

即使`lambda`表达式没有参数，仍然需要提供空括号，就像无参数方法一样：

`() -> { for (int i = 100; i >= 0; i--) System.out.println(i); }`

*函数式接口*

Java 中有很多封装代码块的接口，lambda 表达式与这些接口是兼容的。对于一个只有抽象方法的接口，需要这种接口的对象时，就可以提供一个 lambda 表达式。这种接口称为函数式接口`functional interface`。最好吧 lambda 表达式看作是一个函数，而不是一个对象，另外要接受 lambda 表达式可以传递到函数式接口。

lambda 表达式可以转换为接口，具体的语法很简短

```java
var timer = new Timer(1000, event -> {
    System.out.println("At the tone" + Instant.ofEpochMilli(event.getWhen()));
    Toolkit.getDefalutToolkit().beep();
});
```

*方法引用*

有时，lambda 表达式涉及一个方法。例如，假设你希望只要除夕拿一个定时器时间就打印这个对象。可以调用：

`var timer = new Timer(1000, event -> System.out.pringln(event));`

也可以直接把`println`方法传递到`Timer`构造器就更好了。如下：

`var timer = new Timer(1000, System.out::println);`

`System.out::println`是一个方法引用，它指示编译器生成一个函数式的接口的实例，覆盖这个接口的抽象方法来调用给定的方法。在这个例子中，会生成一个`ActionListener`，它的`actionPerformed(ActionEvent e)`方法要调用`System.out.println(e)`

`::`运算符分隔方法名与对象或类名。主要有 3 种情况：

1. `object::instanceMethod`
2. `Class::instanceMethod`
3. `Class::staticMethod`

在第一种情况下，方法引用等价于向方法传递参数的`lambda`表达式。所以方法表达式等价于`x -> System.out.println(x);`

对于第二种情况，第一个参数会成为方法的隐式参数。例如，`String::compareToIgnoreCase`等同于`(x, y) -> x.compareToIgnoreCase(y)`

第三种情况下，所有参数都传递到静态方法：`Math::pow`等价于`(x, y) -> Math.pow(x, y)`

> 只有当 lambda 表达式的体只调用一个方法而不做其他操作时，才能把 lambda 表达式重写为方法引用

> 如果有多个同名的重载方法，编译器就会尝试从上下文中找出你指的是哪一个方法。

> 有时 API 包含一些专门用作方法引用的方法，例如，Objects 类有一个方法`isNull`，用于测试一个对象引用是否为`null`

> 包含对象的方法引用与等价的 lambda 表达式还有一个细微的差别。考虑一个方法引用，如`separator:equals`。如果`separator`为`null`，构造`separator:equals`就会立即抛出一个`NullPointerException`异常。lambda 表达式`x -> separator.equals(x)`只在调用时才会抛出`NullPointerException`。

可以在方法引用中使用`this`参数。例如`this::equals`等同于`x -> this.equals(x)`。使用`super`也是合法的。

*构造器引用*

构造器引用与方法引用很类似，只不过方法名为`new`。`Person::new`是`Person`构造器的一个引用。哪一个构造器取决于上下文。假设有一个字符串列表，可以把它转换为一个`Person`对象数组，为此要在各个字符串上调用构造器

```java
ArrayList<String> names = ...;
Stream<Person> stream = names.stream().map(Persion::new);
List<Person> people = steam.collect(Collectors.toList());
```

Java 有一个限制，无法构造泛型类型 T 的数组。数组构造器引用对于克服这个限制很有用。表达式`new T[n]`会产生错误，因为这会改为`new Object[n]`。

*变量引用*

通常，你可能希望能够在 lambda 表达式中访问外围方法或类中的变量。考虑下面这个例子：

```java
public static void repeatMessage(String text, int delay) {
    ActionListener listener = event -> {
        System.out.println(text);
        Toolkit.getDefaultToolkit().beep();
    };
	new Timer(delay, listener).start();
}
```

上面例子中有一个问题，lambda 表达式的代码可能在`repeatMessage`调用很久以后才允许，而那时这个参数变量已经不存在了，那如何保留`text`变量呢。lambda 就是闭包，这样`text`的值就回被 lambda 表达式捕获`captured`。

lambda 表达式可以捕获外围作用域中变量的值。在 Java 中，要确保所捕获的值是明确定义的，这里有一个明确的限制。在 lambda 表达式中，只能引用值不会改变的变量。

```java
public static void countDown(int start, int delay) {
    ActionListener listener = event -> {
        start--; // ERROR: Can't mutate captured variable
        System.out.println(start);
    };
    new Timer(delay, listener).start();
}
```

这个限制是有原因的，如果在 lambda 表达式中更改变量，并发执行多个动作时就会不安全。

如果在 lambda 表达式中引用一个变量，而这个变量可能在外部改变，这也是不合法的

```java
public static void repeat(String text, int count) {
    for (int i = l; i <= count; i++) {
		ActionListener listener = event ->  {
            System.out.println(i + ':' + text);
            //ERROR: Cannot refer to changing i
        };
        new Timer(1000, listener).start();
    }
}
```

这里有一条规则：lambda 表达式中捕获的变量必须实际上是事实最终变量`effectively final`。事实最终变量是指，这个变量初始化之后就不会再为它赋新值。在这里，`text`总是指示同一个`String`对选哪个，所以捕获这个变量是合法的。不过`i`值会改变，所以不能捕获

lambda 表达式的体与嵌套块有相同的作用域。这里同样适用命名冲突和遮蔽的有关规则。在 lambda 表达式中声明与一个局部变量同名的参数或局部变量是不合法的

```java
Path first = Path.of("/usr/bin");
Comparetor<String> comp = (first, second) -> first.length() - second.length();
// ERROR: Variable first already defined
```

在一个方法中，不能有两个同名的局部变量，因此，lambda 表达式中同样也不能有同名的局部变量。

在一个 lambda 表达式中使用`this`关键字时，是指创建这个 lambda 表达式的方法的`this`参数。

```java
public class Application {
	public void init() {
        ActionListener listener = event -> {
            // 这里会调用 Application 对象的 toString 方法
            System.out.println(this.toString());
        }
    }
}
```

*处理 lambda 方法*

使用 lambda 表达式的重点是延迟执行`deferred execution`。表示希望以后再执行代码而不是现在直接执行，这有很多原因：

1. 在一个单独的线程中运行代码
2. 多次运行代码
3. 在算法的适当位置运行代码（比如，排序中的比较操作）
4. 发生某种情况时执行代码（比如，点击按钮，数据到达，等）
5. 只在必要时运行代码

下面列出了 JavaAPI 中提供的最重要的函数式接口

| 函数式接口        | 参数类型 | 返回类型 | 抽象方法名 | 描述                         | 其他方法                 |
| ----------------- | -------- | -------- | ---------- | ---------------------------- | ------------------------ |
| Runnable          | 无       | void     | run        | 作为无参数或返回值的动作运行 |                          |
| Supplier<T>       | 无       | T        | get        | 提供一个 T 类型的值          |                          |
| Consumer<T>       | T        | void     | accept     | 处理一个 T 类型的值          | andThen                  |
| BiConsumer<T,U>   | T, U     | void     | accept     | 处理 T 和 U 类型的值         | andThen                  |
| Function<T,R>     | T        | R        | apply      | 有一个 T 类型参数的函数      | compose,andThen,identity |
| BiFunction<T,U,R> | T, U     | R        | apply      | 有 T 和 U 类型参数的函数     | andThen                  |
| UnaryOperator<T>  | T        | T        | apply      | 类型 T 上的一元操作符        | compose,andThen,identity |
| BinaryOperator<T> | T,T      | T        | apply      | 类型 T 上的二元操作符`       | andThen,maxBy,minBy      |
| Predicate<T>      | T        | boolean  | test       | 布尔值函数                   | and,or,negate,isEqual    |
| BiPredicate<T,U>  | T,U      | boolean  | test       | 有两个参数的布尔值函数`      | and,or,negate            |

下表列出了基本类型 `int`、`long`和`double`的 34 个可用的特殊化接口，这些特殊化接口比使用通用接口更高效

| 函数式接口         | 参数类型 | 返回类型 | 抽象方法名   |
| ------------------ | -------- | -------- | ------------ |
| BooleanSupplier    | 无       | boolean  | getAsBoolean |
| PSupplier          | 无       | p        | getAsP       |
| PConsumer          | p        | void     | accept       |
| ObjPConsumer<T>    | T,p      | void     | accept       |
| PFunction<T>       | p        | T        | apply        |
| PToQFunction       | p        | q        | applyAsQ     |
| ToPFunction<T>     | T        | p        | applyAsP     |
| ToPBiFunction<T,U> | T,U      | p        | applyAsP     |
| PUnaryOperator     | p        | p        | applyAsP     |
| PBinaryOperator    | p,p      | p        | applyAsP     |
| Ppredicate         | p        | boolean  | test         |

注：`p`、`q`是`int`、`long`、`double`；`P`、`Q`是`Int`、`Long`、`Double`

> 最好使用上述两个表中的接口，除非你已经有很多有用的方法可以生成 FileFilter 实例

> 大多数标准函数式接口都提供了非抽象方法来生成或合并函数。比如`Predicate.isEqual(a)`等同于`a::equals`

> 如果设计你自己的接口，其中只有一个抽象方法，就可以用`@FunctionalInterface`注解来标记这个方法

**再谈 Comparator**

`Comparator`接口包含很多方便的静态方法来创建比较器。这些方法可以用于 lambda 表达式或方法引用。静态`comparing`方法去一个`键提取器`函数，它将类型`T`映射为一个可比较的类型（如 string）。对要比较的对象应用这个函数，然后对返回的键完成比较。假设有一个`Person`对象数组，可以如下按名字对这些对象进行排序：

`Arrays.sort(people, Comparator.comparing(Person::getName));`

可以把比较器与`thenComparing`方法串起来，来处理比较结果相同的情况

```java
Arrays.sort(people, 
           Comparator.comparing(Person::getLastName)
           .thenComparing(Person::getFistName));
```

如果两个人的姓相同，就会使用第二个比较器

### 内部类

内部类`inner class`是定义在另一个类中的类，为什么要使用内部类呢？主要有两个原因：

- 内部类可以对同一个包中的其他类隐藏
- 内部类方法可以访问定义在这个类作用域中的数据，包括原本私有的数据

内部类原先对于简洁地实现回调非常重要，但现在 lambda 表达式表现更好。但内部类对构建代码还是很有用的。

**使用内部类访问对象状态**

内部类的语法相当复杂。我们选择一个简单但不太实用的例子来说明内部类的使用

```java
public class TalkingClock {
    private int interval;
    private boolean beep;
    public TalkingClock(int interval, boolean beep) { ... }
}

public class TimerPrinter implements ActionListener {
    public void actionPerformed(ActionEvent event) {
        if (beep) Toolkit.getDefaultToolkit().beep();
    }
}
```

可以看到`TimePrinter`类没有实例字段或是名为`beep`的变量，内部类方法可以访问自身的数据字段，也可以访问创建它的外围类对象的数据字段。为此，内部类的对象总是有一个隐式引用，指向创建它的外围类对象的数据字段。这个引用在内部类的定义中是不可见的。

> 我们也可以把`TimePrinter`类声明为私有。只有内部类可以是私有的

**内部类的特殊语法规则**

表达式`OuterClass.this`表示外围类引用，我们可以像下面这样编写`TimePrinter`内部类的`actionPerformed`方法：

```java
public void actionPerformed(ActionEvent event) {
	...
    if (TalkingClock.this.beep) Toolkit.getDefaultToolkit().beep();
}
```

也可以像下面一样更加明确地编写内部类对象的构造器

`ActionListener listener = this.new TimePrinter();`

通常，`this.`限定词是多余的。也可以通过显式的命名将外围类引用设置为其他对象。由于`TimePrinter`是一个公共内部类，对于任意的语音时钟都可以构造一个`TimerPrinter`：

```java
var jabberer = new TalkingClock(1000, true);
TalkingClock.TimePrinter listener = jabberer.new TimePrinter();
```

在外围类的作用域之外，可以这样引用内部类：

`OuterClass.InnerClass`

> 内部类中声明的所有静态字段都必须是`final`，并初始化为要给编译时常量。内部类不能有`static`方法。也可以允许有静态方法，但只能访问外围类的静态字段和方法

**内部类是否有用、必要和安全**

内部类是一个编译器现象，与虚拟机无关。编译器会将内部类转换为常规的类文件，用`$`符号分隔外部类名与内部类名，而虚拟机对此一无所知。比如`TalkingClock`类内部的`TimerPrinter`类将会被转换为类文件`TalkingClock$Time Printer`。可以对这个类完成反射，也可以简单地使用`javap`工具

`java -private ClassName`

**局部内部类**

可以在一个方法中定义一个类，它的作用域会限定在声明这个类的块中。除了这个方法，没有任何方法知道这个类的存在

```java
public void start() {
    class TimPrinter implements ActionListener {
        //...
    }
}
```

**由外部方法访问变量**

局部类不仅能够访问外部类的字段，还能访问局部变量。不过那些局部变量必须是事实最终变量`effectively final`，下面是一个示例

```java
public void start(int interval, boolean beep) {
    class TimerPrinter implements ActionListener {
        public void actionPerformed(ActionEvent event) {
			if (beep) Toolkit.getDefaultToolkit().beep()
        }
    }
}
```

上述例子中，`TimPrinter`类会在`beep`参数值消失之前必须将`beep`字段赋值给`start`方法的局部变量。所以能够使用`beep`变量

**匿名内部类**

假如只想创建这个类的一个对象，甚至不需要为类指定名字。这样的一个类被称为内部类`anonymous inner class`

```java
public void start(int interval, boolean beep) {
    class TimerPrinter implements ActionListener {
        public void actionPerformed(ActionEvent event) {
			if (beep) Toolkit.getDefaultToolkit().beep()
        }
    }
}
```

这个语法的含义是：创建一个类的新对象，这个类实现了`ActionListener`接口、需要实现的方法`actionPerformed`在括号`{}`内定义

一般语法如下

```java
new SuperType(construction parameters) {
	// inner class methods and data
}
```

`SuperType`可以是接口，如`ActionListener`，如果是这样，内部类就要实现这个接口。`SuperType`也可以是一个类，如果是这样，内部类就要扩展这个类。

**静态内部类**

有时使用内部类只是为了把一个类隐藏在另外一个类的内部，并不需要内部类有外围类对象的一个引用。为此，可以将内部类声明为`static`，这样就不会生成那个引用。

下面是一个想要使用静态内部类的典型例子，考虑这样一个任务：计算数组中的最小值和最大值。可以编写两个方法，一个用于计算最小值，一个用于计算最大值。在调用这两个方法时，数组被遍历两次，如果只遍历一次，同时计算出最大值和最小值，就可以大大提高效率

```java
double min = Double.POSITIVE_INFINITY;
double max = Double.NEGATIVE_INFINITY;
for (double v : values) {
    if (min > v) min = v;
    if (max < v) max = v;
}
```

可以使用一个内部类用于解决可能的命名冲突，声明为`static`可以不生成其他对象的引用：

```java
class ArrayAlg {
    public static class Pair {
        // ...
    }
}

// 可通过以下方式访问
ArrayAlg.Pair p = ArrAlg.minmax(d);
```

只要内部类不需要访问外围类对象，就应该使用静态内部类。与常规内部类不同，静态内部类可以有静态字段和方法。在接口中生命的内部类自动是`staticl`和`public`

**服务加载器**

通常提供一个服务时，程序希望服务设计者能够有一些自由，能够确定如何实现服务的特性。另外还希望有多个实现以供选择。利用`ServiceLoader`类可以很容易地加载一个公共接口的服务。

### 代理

利用代理可以在运行时创建实现了一组给定接口的新类。只有在编译时期无法确定需要实现哪个接口时才有必要使用代理。

**何时使用代理**

假设你想构造一个类的对象，这个类实现了一个或多个接口，但在编译时你可能并不知道这些接口到底是什么。代理机制是一种很好的解决方案。代理类可以在运行时创建一个全新的类。这样的代理类能够实现你指定的接口。代理类包含以下方法：

- 指定接口所需要的全部方法
- Object 类中的全部方法，例如，`toString`、`equals`等

不过，不能在运行时为这些方法定义新代码。必须提供一个调用处理器`invocation handler`。调用处理器是实现了`InvocationHandler`接口的类的对象。这个接口只有一个方法：

`Object invoke(Object proxy, Method method, Object[] args)`

无论何时调用代理对象的方法，调用处理器的`invoke`方法都会被调用，并向其传递`Method`对象和原调用的参数。之后调用处理器必须确定如何处理这个调用。

**创建代理对象**

要想创建一个代理对象，需要使用`Proxy`类的`newProxyInstance`方法。这个方法有三个参数：

- 类加载器`class loader`
- 一个`Class`对象数组，每个元素对应需要实现的各个接口
- 一个调用处理器

使用代理可能处于很多目的：

- 将方法调用路由到远程服务器
- 在运行的程序中将用户界面事件与动作关联起来
- 为了调试，跟踪方法调用

**代理类的特性**

代理类是在程序运行过程中动态创建的，但是，一旦被创建，它们就成了常规类，与虚拟机中的任何其他类没有区别

所有代理类都扩展`Proxy`类。一个代理类只有一个实例字段——即调用处理器，它在`Proxy`超类中定义。

所有代理类都要覆盖`Object`类的`toString`、`equals`和`hashCode`方法。

没有定义代理类的名字，Oracle 虚拟机中的`Proxy`类将会生成一个以字符串`$Proxy`开头的类名。

对于一个特定的类加载器和预设的一组接口来说，只能有一个代理类

代理类总是`public`和`final`。如果代理类实现的所有接口都是`public`，这个代理类就不属于任何特定的包。

可以通过调用`Proxy`类的`isProxyClass`方法检测一个特定的`Class`对象是否表示一个代理类



