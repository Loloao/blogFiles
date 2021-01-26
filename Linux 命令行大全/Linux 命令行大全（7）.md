## 流控制：WHILE 和 UNTIL 循环

如果能够用某种方法重构程序，让程序能够一遍一遍地展示菜单选项，直到用户选择退出程序，这样会好得多

### WHILE

按顺序显示`1~5`这 5 个数字，就可以构建这样的 bash 脚本

```shell
count=1

while [ $count -le 5 ]; do
	echo $count
	count=$((count + 1))
done
echo "Finished."
```

`while`命令的语法结构如下

`while commands; do commands; done`

我们可以使用`while`循环来改进上一章的菜单程序

```shell
DELAY=3 # 延时多久显示结果
while [[ $REPLY != 0 ]]; do
    clear
    echo "
    Please Select:

    1. Display System Information
    2. Display Disk Space
    3. Display Home Space Utilization
    0. quit
    "

    read -p "Enter selection [0-3] > "

    if [[ $REPLY =~ ^[0~3]$ ]]; then
        if [[ $REPLY == 1 ]]; then
            echo "Hostname: $HOSTNAME"
            uptime
            sleep $DELAY
        fi
        if [[ $REPLY == 2 ]]; then
            df -h
            sleep $DELAY
        fi
        if [[ $REPLY == 3 ]]; then
            if [[ $(id -u) -eq 0 ]]; then
                echo "Home Space Utilization (All Users)"
                du -sh /home/*
            else
                echo "Home Space Utilization ($USER)"
                du -sh $HOME
            fi
            sleep $DELAY
        fi
    else
        echo "Invalid entry."
        sleep $DELAY
    fi
done
echo "Program terminated."
```

### 跳出循环

bash 提供了两种可用于控制循环内部程序流的内建命令。其中`break`命令立即终止循环，程序从循环后的语句恢复执行。`continue`命令则会导致程序跳过循环剩余的部分，直接开始下一次迭代循环。下面这个菜单程序使用了`break`和`continue`

```shell
DELAY=3 # 延时多久显示结果
while true; do
    clear
    echo "
    Please Select:

    1. Display System Information
    2. Display Disk Space
    3. Display Home Space Utilization
    0. quit
    "

    read -p "Enter selection [0-3] > "

    if [[ $REPLY =~ ^[0~3]$ ]]; then
        if [[ $REPLY == 0 ]]; then
            echo "Program terminated."
            break
        fi
        if [[ $REPLY == 1 ]]; then
            echo "Hostname: $HOSTNAME"
            uptime
            sleep $DELAY
            continue
        fi
        if [[ $REPLY == 2 ]]; then
            df -h
            sleep $DELAY
            continue
        fi
        if [[ $REPLY == 3 ]]; then
            if [[ $(id -u) -eq 0 ]]; then
                echo "Home Space Utilization (All Users)"
                du -sh /home/*
            else
                echo "Home Space Utilization ($USER)"
                du -sh $HOME
            fi
            sleep $DELAY
            continue
        fi
    else
        echo "Invalid entry."
        exit 1
    fi
done
echo "Program terminated."
```

### until

`while`命令推出状态不为 0 时终止循环，而`until`命令则相反。除此之外，`until`命令与`while`命令很相似，比如输出小于 5 的变量

```shell
count=1

until [ $count -gt 5 ]; do
	echo $count
	count=$((count + 1))
done
echo "Finished."
```

## 故障诊断

随着脚本的复杂度越来越高，当脚本出现错误或执行情况和预期不同时，就需要看是哪里出的问题。

### 语法错误

语法错误是一种常见的错误类型，其中就包括了`shell`语句中一些元素的拼写错误。在大多数情况下，`shell`会拒绝执行此种类型错误的脚本

```shell
number=1
if [ $number = 1 ]; then
	echo "Number is equal to 1."
else
	echo "Number is not equal to 1."
fi
```

**引号缺失**

现在修改上述脚本，删除第一个`echo` 命令后实参后的双引号

```shell
number=1
if [ $number = 1 ]; then
	# 下一行末尾缺少一个双引号
	echo "Number is equal to 1.
else
	echo "Number is not equal to 1."
fi
```

观察出现的现象如下所示

```shell
t/home/me: line 10: unexpected EOF while looking for matching '"'
t/home/me: line 13： syntax error: unexpected end of file
```

此删除操作使脚本产生了两个错误。但是错误报告指出的行并不是之前删除双引号所在的行，而是之后的代码。当系统读取到所删除双引号的位置之后，bash 将继续向下寻找与前双引号对应的引号，这样的行为会一直延续到 bash 找到目标，也就是第二个`echo`命令后的第一个引号处。

这种类型的错误在长脚本中很难发现，但使用带有语法结构突出显示功能的编辑器能够帮助找到这类错误。比如配备的是完整版 vim，可以使用以下命令启用 vim 的语法结构

`:syntax on`

**符号缺失冗余**

```shell
number=1
# 缺少分号
if [ $number = 1 ] then
	echo "Number is equal to 1."
else
	echo "Number is not equal to 1."
fi
```

结果如下：

```shell
t/home/me: line 9: syntax error near unexpected token 'else'
t/home/me: line 9：'else'
```

这个现象的机理非常有趣。实际上，`if`接受一些列的明亮，并评估系列中最后一个命令为退出码。在本程序中，这个命令系列只由一条命令组成，·`[,`即`test`的同义表示，`[`命令将之后的部分视为一系列的参数——也就是本例中的`$number`，`=`，`1`和`]`。在分号被删除之后，`then`，`echo`也是合法的，但`echo`被翻译为`if`命令的退出码。最后遇到的就是`else`，但`else`不应该出现在这里

**非预期的展开**

有一些脚本错误是间歇出现的，脚本有时候会运行正常，有时候又会因为展开的结果而出错。现在将缺失的分号补回来并改变`number`的值为空

```shell
# number 值为空
number=

if [ $number = 1 ]; then
	echo "Number is equal to 1."
else
	echo "Number is not equal to 1."
fi
```

输出为

```shell
t/home/me: line 7: [: =: unary operator expected]
```

造成这问题的原因是`test`命令中`number`变量的展开，当命令

`[ $number = 1 ]`的`expansion`情形为`number`变量为空，就造成了这样的情况

`[ = 1 ]`

等式无效，就产生了错误。之后因为`test`不成立，`if`命令接受到了一个非零的退出码，从而执行了第二个`echo`命令。

### 逻辑错误

与语法错误不同的是，逻辑错误不会阻碍脚本的运行，因为脚本包含的逻辑问题，脚本尽管可以运行但是不能产生理想的结果。可能发生的逻辑错误有非常多种，但是以下几种逻辑错误是脚本中最常见的

- 条件表达错误。编写代码中，很容易写出不正确的`if`、`then`和`else`语句，造成错误的逻辑，例如相反的或不完整的逻辑表达
- 从 1 开始错误。在使用计数器的循环中，可能会忽略程序需要从 0 而不是 1 开始计数才能在正确的点结束循环，这种错误可能导致循环次数过多，超过了预期的终点，或循环次数过少，缺失了最后一次的迭代过程
- 非预期的情形。大多数此种错误来源于程序运行过程中遇到了编写程序人员没有预期到的数据或环境，比如一个包含了空格的文件名本应该作为单个文件名存在，却扩展成为了多个命令的实参

**防御编程**

在编程中核实各项假设是很重要的事情，这意味着我们需要对程序的输出状态以及脚本所用命令进行仔细评估。比如下面一段脚本

```shell
cd $dir_name
rm *
```

只要名为`dir_name`的目录存在，以上两行代码并没有什么本质性的错误。但是如果目标不存在就会继续读取下一段，之后会删除当前的工作目录

我们可以进行一些改进

`cd $dir_name && rm *`

这样一来，`cd`命令跳转失败，`rm`命令就不会执行，但是仍然存在当`dir_name`为空的情况下，用户主目录被删除的可能性，检查`dir_name`是否确实包含有效目录可避免此情形

`[[ -d $dir_name ]] && cd $dir_name && rm *`

通常来说，发生了上述情形之一，最好立刻终止脚本的运行

```shell
if [[ -d $dir_name ]]; then
	if cd $dir_name; then
		rm *
	else
		echo "cannot cd to '$dir_name'" >&2
		exit 1
	fi
else
	echo "no such directory: '$dir_name'" >&2
	exit 1
fi
```

以上代码中，不仅检查`dir_name`是否包含有效的目录名，且验证了`cd`命令是否跳转成功。

**输入值验证**

一条普遍适合的编程法是若程序需获得用户输入，则程序必须能够处理任何输入值。这通常意味着必须检查输入值，以保证有效的输入可用于进一步的处理过程，比如

`[[ $REPLY =- ^[0-3]$ ]]`

### 测试

对任何软件的开发过程来说，包括脚本的开发过程，测试都是一个非常重要的步骤。通过尽早发布和经常发布新版本，软件就能得到更多用户的使用和测试

**桩**

从脚本开发的最初阶段开始，`桩(stub)`就是可用于查看工作进程的重要技术手段

** 测试用例**

开发和应用高质量的测试用例是执行测试过程终点重要组成部分。这就要求测试人员要很小心地选择能反映边缘测试的输入数据和操作条件，在上面的例子中，我们需要测试以下三种特定条件下代码的执行情况

- `dir_name`包含的是存在的目录名
- `dir_name`包含的是不存在的目录名
- `dir_name`为空

### 调试

如果测试揭露出脚本存在问题，那么下一步就是调试。“问题”通常意味着，在某些情况下，脚本的运行结果和预期的效果不同。

**找到问题域**

在一些长脚本中，将问题相关的脚本域隔离出来是很有必要的。其中一种能够用于隔离代码片段的方法是"注释"掉脚本的一部分

**追踪**

bug 还经常表现为脚本中异常单逻辑流程。也就是说，脚本的一部分或从来没有执行过，或是以错误的顺序或在错误的时间执行了。追踪是一种用于查看程序实际运行流程的技术

比如在代码片段中添加一些信息

```shell
echo "preparing to delete file" >&2
if [[ -d $dir_name ]]; then
	if cd $dir_name; then
echo "deleting files" >&2
		rm *
	else
		echo "cannot cd to '$dir_name'" >&2
		exit 1
	fi
else
	echo "no such directory: '$dir_name'" >&2
	exit 1
fi
echo "file deletion complete" >&2
```

bash 也提供了一种追踪的方法，即直接使用`-x`选项或是`set`命令加`-x`选项

```shell
# 加 -x 选项是直接跟踪整个文件的流程
# !/bin/bash -x
if [[ -d $dir_name ]]; then
	if cd $dir_name; then
		rm *
	else
		echo "cannot cd to '$dir_name'" >&2
		exit 1
	fi
else
	echo "no such directory: '$dir_name'" >&2
	exit 1
fi
```

`set`命令加`-x`选项是对脚本选的的一部分而不是整个脚本执行追踪

```shell
# !/bin/bash -x
# 使用 -x 激活追踪
set -x # Turn on tracing
if [[ -d $dir_name ]]; then
	if cd $dir_name; then
		rm *
	else
		echo "cannot cd to '$dir_name'" >&2
		exit 1
	fi
else
	echo "no such directory: '$dir_name'" >&2
	exit 1
fi
# 使用 +x 解除追踪
set +x # Turn off tracing
```

## 流控制：case 分支

### case

bash 的多项选择复合命令被称为`case`

```shell
case word in
	[pattern [| pattern]...) commands ;;]...
esac
```

使用`case`我们可以简化提到过的菜单脚本

```shell
clear
echo "
Please Select:

1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. quit

read -p "Enter selection [0-3] > "

case $REPLY in
	0)	echo "Progress terminated."
		exit
		;;
	1)	echo "Hostname: $HOSTNAME"
		uptime
		;;
	2)	df -h
		;;
	3)	if [[ $(id -u) -eq 0 ]]; then
			echo "Home Space Utilization (All Users)"
			du -sh /home/*
		else
			echo "Home Space Utilization ($USER)"
			du -sh $HOME
		fi
		;;
	*)	echo "Invalid entry" >&2
		exit 1
		;;
esac
```

**模式**

同路径名展开一样，`case`使用以`)`字符结尾的模式。下面是一些有效的模式

- `a)`：关键字为`a`则吻合
- `[[:alpha:]])`：关键字为单个字母则吻合
- `???)`：关键字为三个字符则吻合
- `*.txt)`：关键字`.txt`结尾则吻合
- `*)`：与任何关键字吻合。在`case`命令的最后一个模式应用此项是个不错的做法

**多个模式的组合**

我们可以使用竖线作为分隔符来组合多个模式，模式之间是或的条件关系。这对一些类似处理大写和小写字母的事件很有帮助

## 位置参数

本章节将讲解允许程序访问命令行内容的`shell`功能

### 访问命令行

shell 提供了一组名为位置参数的变量，用于储存命令行终点关键字，这些变量分别名为`0~9`，可通过以下方法展示这些变量

```shell
# posit-param: script to view command line parameters
echo "
\$0 = $0
\$1 = $1
\$2 = $2
\$3 = $3
\$4 = $4
\$5 = $5
\$6 = $6
\$7 = $7
\$8 = $8
\$9 = $9
"
```

这个简单的脚本展示了从变量`$0`到`$9`的值，在没有任何命令行实参的情形下执行此脚本结果如下，`posit-param`

```shell
\$0 = /home/me/bin/posit-param
\$1 = 
\$2 = 
\$3 = 
\$4 = 
\$5 = 
\$6 = 
\$7 = 
\$8 = 
\$9 = 
```

即使没有提供任何参数，变量`$0`总是会储存有命令行显示的第一行数据，也就是所执行程序所在的路径名，现在提供实参`posit-param a b c d`

```shell
\$0 = /home/me/bin/posit-param
\$1 = a
\$2 = b
\$3 = c
\$4 = d
\$5 = 
\$6 = 
\$7 = 
\$8 = 
\$9 = 
```

使用参数扩展技术，用户实际可以获取多于 9 个的参数，为标明一个大于 9 的数字，将数字用大括号括起来，如`${10}`，`${55}`和`${211}`等

  **shift 处理大量的实参**

但如果给程序提供大量的实参时，只会显示前 10 条数据，但是按`shift`之后`$0`后的变量从`$1`开始就会变成下一位的值。

**简单的应用程序**

不使用`shift`，我们也可以写出使用未知参数的有用的应用程序。

```shell
PROGNAME=$(basename $0)

if [[ -e $1 ]]; then
	echo -e "\nFile Type:"
	file $1
	echo -e "\nFile Status:"
	stat $1
else
	echo "$PROGNAME: usage: $PROGNAME file" >&2
	exit 1
fi
```

这个程序输出了单个特定文件的文件类型（来自`file`命令）和文件状态（来自`stat`命令）。程序中的`PROGRNAME`变量是一个有趣的角色，其值来自于`basename $0`命令的执行结果。`basename`命令的作用是移除路径名的其实部分，只留下基本的文件名。

**在 shell 函数中使用位置参数**

就像位置参数可用于向 shell 脚本传递实参一样，位置参数也可用于函数实参的传递，现在讲`file_info`脚本改写为`shell`函数

```shell
file_info () {
	if [[ -e $1 ]]; then
        echo -e "\nFile Type:"
        file $1
        echo -e "\nFile Status:"
        stat $1
    else
        echo "$PROGNAME: usage: $PROGNAME file" >&2
        exit 1
    fi
}
```

### 处理多个位置参数

有时我们将所有位置参数作为一个整体处理会比较方便。比如，为目标程序写一个包装，也就是编写一个简化了目标程序运作过程的脚本或 shell 函数的时候，包装就供应了一系列的复杂的命令行选项，并为下一层的程序传递所需的实参

shell 为这项功能提供了两种特殊的参数。两种参数都能够扩展为一个完整的位置参数列，但是又有着微妙的区别。

`$*`：可扩展为从 1 开始的位置参数列。当包括在双引号内时，扩展为双引号引用的由全部位置参数构成的字符串，每个位置参数以 IFS shell 变量的第一个字符（默认情况下为空格）间隔开

`$@`：可扩展为从 1 开始的位置参数列。档包括在双引号内时，将每个位置参数扩展为双引号引用的单独单词

## for 循环

本章是关于流控制的最后一章，我们将会再学习一个新的 shell 循环结构，因为 for 循环采用在循环期间进行序列处理的机制，所以它不同于`while`循环和`until`循环。

### for: 传统 shell 形式

原始的`for`命令语法如下

```shell
for variable [in words]; do
	commands
done
```

其中，`variable`是一个在循环执行时会增值的变量名，`words`是一列将按顺序赋给变量`variable`的可选项，`commands`部分是每次循环时都会执行的命令

`for`循环真正强大的功能在于创建字符列表的方式有多种，例如，可以使用花括号扩展方式，比如

`for i in {A..D}; do echo $i; done`

或使用路径名扩展方式

`for i in distros*.txt; do echo $i; done`

### for C语言形式

最近的 bash 版本计入了第二种`for`命令语法，它类似于 C 语言形式，并且许多其他编程语言都支持这种形式

```shell
for (( expression1; expression2; expression3 )); do
	commands
done
```

其中`expression1`，`expression2`和`expression3`为算术表达式，`commands`是每次循环都要执行的命令

就结果形式而言，这种形式等同于如下结构

```shell
(( expression1 ))
while (( expression2 ));do
	commands
	(( expression3 ))
done
```

这里有个典型的应用

```shell
for ((i=0; i<5; i=i+1));do
	echo $i
done
```

## 字符串和数字

本章将学习几个用于操纵字符串和数字的`shell`脚本特性。

### 参数扩展

**基本参数**

参数扩展的最简单形式体现在平常对变量的使用中。举例来说，`$a`扩展后成为变量`a`所包含的内容，无论`a`包含什么。简单参数也可以被括号包围，例如`${a}`。这对扩展本身毫无影响，但是，当变量相邻与其他文本时，则必须使用括号，佛则可能让`shell`混淆。下面这例子，我们试图以附加字符串`_file`到变量`a`内容后的方式新建一个文件名

`a ="foo";echo "$(a)_file"` => `foo_file`

**空变量数组的管理**

有的参数扩展用于处理不存在的变量和空变量，这些参数扩展在处理缺失的位置参数和给参数赋默认的值时很有用处。这种参数扩展形式如下：

`${parameter:-word}`

如果`parameter`未被设定或是空参数，则其扩展为`word`的值；如果`parameter`非空，则扩展为`parameter`的值

`foo=;echo ${foo:-"a"}` => `a`

`echo $foo;foo=bar;echo ${foo:-"a"}` => `bar`

位置参数和其他特殊参数不能以这种方式赋值

我们也可以使用`=`代替连字符`-`

我们使用一个问号，如下所示

`${parameter;?word}`

如果`parameter`未被设定为空，这样扩展会致使脚本出错而退出，并且`word`内容输出到标准错误，如果`parameter`非空，则扩展为`parameter`的值

如果我们使用一个加号，则如果不产生任何扩展。如果`parameter`非空，则`word`的值将取代`parameter`的值；然而`parameter`的值并不发生变化

**返回变量名的扩展**

shell 具有返回变量名的功能。这种功能在相当特殊的情况下才会被用到，该扩展返回当前以`prefix`开头的变量名

```shell
${!prefix*}
${!prefix@}
```

**字符串操作**

对字符串的操作，存在着大量的扩展集合。其中一些扩展尤其适用于对路径名的操作。扩展式

`${#parameter}`

扩展为`parameter`内包含的字符串的长度，一般来说，参数`parameter`是个字符串。然而，如果参数`parameter`是`@`或`*`，那么扩展结果就是位置参数的个数

`foo='a';echo "'$foo' is ${#foo} characters long."` => `'This string is long.' is 20 characters long.`

`${parameter:offset}`

`${parameter:offset:length}`

上面两个扩展用于提取一部分包含在参数`parameter`中的字符串。扩展以`offset`字符开始，直到字符的末尾，除非`length`特别指定，如果`offset`为负，则默认表示它从字符串末尾开始，而不是字符串开头，注意负值前必须有一个空格，以防和扩展混淆，`length`长度不能小于 0

如果参数为`@`，扩展的结果从`offset`开始，`length`为位置参数

`${parameter#pattern}`

`${parameter##pattern}`

根据`pattern`定义，这些扩展去除了包含在`parameter`中的字符串的主要部分。`pattern`是一个通配符模式，类似那些勇于路径名的扩展。两种形式的区别在于`#`形式去除最短匹配，而`##`形式去除最长匹配最长匹配

`${parameter%pattern}`

`${parameter%%pattern}`

这些扩展与上述的`#`和`##`扩展相同，除了一点——它们从参数包含的字符串末尾去除文本，而非字符串开头

`${parameter/pattern/string}`

`${parameter//pattern/string}`

`${parameter/#pattern/string}`

`${parameter/%pattern/string}`

这个扩展在`parameter`的内容上搜索和替换非常有效。如果文本被发现和通配符`pattern`一致，就被替换为`string`的内容。`//`表示所有`pattern`被替换，`/#`要求匹配出现在现在字符串开头，`/%`要求匹配出现在字符串末尾，`/string`可以省略，不过和`pattern`匹配的文本都会被删除

### 算术计算和扩展

**数字进制**

在算术表达式中，shell 支持任何进制表示的整数。下面列出了基本数字进制的描述

- `Number`：默认情况下，`number`没有任何符号，将作为十进制数字
- `0number`：在数字表达式中，以`0`开头的数字被认为是八进制数字
- `0xnumber`：十六进制的符号
- `base#number`：`base`进制的`number`

`echo $((0xff))` => `255`

**一元运算符**

有两种一元运算符：`+`和`-`

**简单算术**

| 操作符 | 描述         |
| ------ | ------------ |
| +      | 加法         |
| -      | 减法         |
| *      | 乘法         |
| /      | 整除         |
| **     | 求幂         |
| %      | 取模（余数） |

由于`shell`的算术运算符仅适用于整数，除法的结果永远是完整的数字，如下

`echo $(( 5 / 2 ))` => `2`

**赋值**

尽管算术表达式并非立等可见，但是它们可以用来赋值。算术表达式可以这样使用

除了`=`之外，`shell`还提供了一些相当有用的赋值语句

`+=`，`-=`，`*=`，`%=`，`++`，`--`

**位操作**

有一种操作符以一种非比寻常的方式巧妙地进行数字运算，这些操作符在位层面执行运算。它们被用于特定的低层任务中，常用来位标志的设置和读取

| 操作符 | 描述     |
| ------ | -------- |
| ~      | 按位取反 |
| <<     | 逐位左移 |
| >>     | 逐位右移 |
| &      | 按位与   |
| \|     | 按位或   |
| ^      | 按位异或 |

除了按位取反之外，也存在相应的赋值操作（例如：`<<=`）

**逻辑操作**

`(( ))`复合命令支持多种比较操作，有另外几种可以被判断逻辑是否成立的操作

| 操作符            | 描述               |
| ----------------- | ------------------ |
| <=                | 小于或等于         |
| >=                | 大于或等于         |
| <                 | 小于               |
| >                 | 大于               |
| ==                | 等于               |
| !=                | 不等于             |
| &&                | 逻辑与             |
| \|\|              | 逻辑或             |
| expr1?expr2:expr3 | 比较（三元组）操作 |

### bc: 一种任意精度计算语言

`shell`可以处理所有种类的整数运算，但是如果需要执行更高级的数学运算，或使用浮点数怎么办呢？答案是无法实现。为了达到这个目的，我们需要使用一个外部程序。这里有几种方法可以使用，如嵌入`Perl`或`AWK`是一个可能的解决方案。但这已超过本书范围，我们可以使用一个专门的计算器程序，大多数 Linux 系统都支持这种程序`bc`

`bc`程序读取一个使用类 C 语言编写的程序文件，并执行它。`bc`脚本可以是一个单独的文件，也可以从标准输入中读取。它支持很多功能，包括变量、循环以及程序员自定义的函数。我们这里只是抛砖引玉

**bc 的使用**

比如一个`bc`脚本保存为`foo.bc`

`bc -q foo.bc` => `4`

`bc`也可交互使用，只需简单地输入运算之，计算结果就会被显示出来，使用 bc 命令中的`quit`结束交互回话

## 数组

在本章中，我们将会学习一种包含多个值的数据结构——数组

### 创建一个数组

`a[1]=foo`此时将`foo`赋给数组`a`的元素`1`，使用`declare`也可以创建数组

`declare -a a`

### 数组赋值

赋值方式有两种，一种是上文提到的

`name[subscript]=value`

使用下面的方法可以赋多值

`name=(value1 value...)`

`days=(Sun Mon Tue Wed Thu Fri Sat)`

或是指定下标

`days=([0]=Sun [1]=Mon [2]=Tue [3]=Wed [4]=Thu [5]=Fri [6]=Sat)`

### 数组操作

**输出数组的所有内容**

我们可以使用下标`*`和`@`来访问数组中的每个元素，对于定位参数来讲，符号`@`较之更有用

`animals=("a dog" "a cat" "a fish");for i in ${animals[*]}; do echo $i; done`

**确定数组元素的数目**

使用参数扩展，我们可以采用类似获取字符串长度的方式来确定数组中元素的数目

`a[100]=foo;echo ${#a[@]}` => `1` 输出数组元素数量

`echo ${#a[100]}`输出位于 100 位置的元素长度

**查找数组中使用的下标**

由于 bash 允许在下标赋值中包含“空格”，有时这对确定实际存在的元素是很有用的。这可以通过参数扩展来实现

`${!array[8]}`

`${!array[@]}`

这里的`array`是数组变量名

`for i in "${foo[@]}";do echo $i;done`

**在数组的结尾增加元素**

如果在数组的结尾需要添加元素的话，知道数组中元素的数目是没有用的，我们并不知道我们使用的最大数组索引是什么。`shell`提供了一种解决方法，通过使用`+=`赋值运算符，在数组的尾部自动添加元素

`foo=(a b c);foo+=(d e f)`

**数组的删除操作**

使用`unset`命令，我们可以删除数组，如下

`foo=(a b c d e f);unset foo`

我们也可以使用`unset`来删除单个数组元素

`unset 'foo[2]'`

另外注意对数组元素赋一个空值并不意味着清空它的内容

## 其他命令

### 组命令和子 shell

bash 允许将命令组合到一起使用，有两种方式，第一种是利用组命令，另一种是使用子`shell`

组命令：

`{ command1; command2; [command3; ...] }

子`shell`：

`( command1; command2; [command3; ...])`

这两种形式的区别在于，组命令使用花括号将其命令括起来，而子`shell`则使用圆括号

**执行重定向**

组命令和子`shell`都可以用来管理重定向

`{ ls -l; echo "Listing of foo.txt"; cat foo.txt } > output.txt`

当创建命令管道时，将多条命令的结果输出到一条流中

**进程替换**

组命令和子`shell`有一处主要的不同，子`shell`只在当前`shell`的子拷贝中执行命令，而组命令是在当前`shell`中执行所有命令，这意味着子`shell`复制当前的环境变量以创建一个新的`shell`实例。当子`shell`退出时，复制的环境变量也就消失了。所以大多数情况下，除非脚本需要子`shell`，否则组命令比子`shell`更可取。组命令更快且需要更少的内存

在使用`read`命令时，`read`命令是在子`shell`中执行的，并且当子`shell`终止的时候，`REPLY`的拷贝也遭到了破坏，此时我们可以使用进程替换，它有两种方式，一种是产生标准输出的进程

`<(list)`

一种是吸纳标准输入的进程，如下所示

`>(list)`

这里的`list`是一系列命令

我们可以这样使用`read < <(echo "foo");echo $REPLY`，此时输出正确，进程替换通常结合带有`read`的循环使用

### trap

拥有一个信号处理程序可能对庞大复杂的脚本大有裨益

`trap`命令使用的语法如下

`trap argument signal [signal...]`

这里的`argument`是作为命令被读取的字符串，而`signal`是对信号量的说明，该信号量会触发解释命令的执行

`trap "echo 'I am ignoring you.' " SIGINT SIGTERM`

每当运行中的脚本接受到`SIGINT`或是`SIGTERM`信号时，脚本定义的`trap`将执行`echo`命令

> 临时文件

在脚本中使用信号控制句柄，是为了删除脚本执行过程中用于保存中间变量的临时文件。传统意义上来书，类 UNIX 系统中的程序是在`/tmp`目录里创建临时文件，该目录是用于保存临时文件的共享目录

### 异步执行

有时我们希望同时执行多项任务，bash 提供了一个内置的命令来帮助管理异步执行。`wait`命令可以让父脚本暂停，直到指定的进程（比如子脚本）结束

**wait命令**

```shell
# 父脚本
echo "Parent: starting..."
echo "Parent: launching child script..."
async-child &
# 通过将 $! shell 程序参数值赋给 pid 变量来记录子脚本的进程 ID
pid=$!
echo "Parent: child (PID= $pid) launched."

echo "Parent: continuing..."
sleep 2

echo "Parent: pausing to wait for child to finish..."
wait $pid

echo "Parent: child is finished. Continuing..."
echo "Parent: parent is done. Exiting."

# 子脚本
echo "Child: child is running..."
sleep 5
echo "Child: child is done. Exiting."
```

### 命名管道

使用命名管道可以建立两个进程之间的通信，并且可以像其他类型的文件一起使用，可以以如下方式设置

`process1 > named_pipe`

以及

`process2 < name_pipe`

执行效果等同于

`process1 | process2`

**设置命名管道**

通过`mkfifo`命令完成

`mkfifo pipe1`

**使用命名管道**

`ls -l > pipe1`此时命名管道被阻塞，当一个进程连接到管道的另一端时，进程会从管道中读取数据

`cat < pipe1`

