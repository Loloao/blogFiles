## 编写 shell 脚本

我们可以通过自行设计，将命令行组合成程序的方式，shell 就可以独立完成一系列复杂的任务

### 什么是 shell 脚本

最简单的解释是，`shell`脚本是一个包含一系列命令的文件，`shell`读取这些这个文件，然后执行这些命令，就好像这些命令是直接输入到命令行中一样

### 怎样写 shell 脚本

1. 编写脚本。`shell`脚本是普通的文本文件，所以我们需要一个文本编辑器来编辑它，最好的文本编辑器可以提供"语法高亮"的功能，从而能够看到脚本元素彩色代码视图.
2. 使脚本可执行。系统相当严格，它不会将任何老式的文本文件当做程序。所以我们需要将脚本文件的权限设置为允许执行
3. 将脚本放置在`shell`能够发现的位置。当没有显式指定路径名时，`shell`会自动地寻找某些目录，来查找可执行文件。为了最大程度的方便，我们会将脚本放置在这些目录下

**脚本文件的格式**

在文本行中，`#`后所有的内容会被忽略

```shell
#!/bin/bash
# This is our first script
echo 'Hello World！'
```

**可执行权限**

对于脚本，有两种常见的权限设置：权限为 755 的脚本，所有人都可以执行。权限为 700 的脚本，只有脚本所有人才能执行，为了能够执行脚本，它必须是可读的

**脚本文件的位置**

如果没有显式地指定路径，则系统在查找一个可执行程序时，需要搜索一系列目录。这就是当我们在命令行中输入`ls`时，系统知道要执行`/bin/ls`的原因。`/bin`目录是系统会自动搜索的一个目录

**脚本的理想位置**

`~/bin`目录是一个存放个人使用脚本的理想位置。如果我们编写了一个系统上所有用户都可以使用的脚本，则该脚本的传统位置是`/usr/local/bin`系统管理员使用的脚本通常放置在`/usr/local/sbin`。大多数情况下，本地支持的软件，无论是脚本或是编译好的程序，应该放置在`/usr/local`目录下，而不是`/bin`或是`/usr/bin`目录下。

### 更多的格式诀窍

之所以严肃认真地编写脚本，其中一个目的是为维护提供遍历

**长选项名**

我们学习的很多命令都有短选项名和长选项明，比如，在命令行输入选项时，短选项更可取，但是在编写脚本时，长选项名可以提高可读性

**缩进和行连接**

在使用长选项命令时，将命令扩展为好几行，可以提高命令的可读性，比如一个特别长的`find`命令示例

```shell
find playground \( -type f -not -perm 0600 -exec chmod 0600 '{}' ';' \) -or \( -type d -not -perm 0700 -exec chmod 0700 '{}' : ';' \)
```

在脚本中，如果以下列形式编写，显然可读性更高

```shell
find playground \
	\( \
		-type f \
		-not -perm 0600 \
		-exec chmod 0600 '{}' ';' \
	\) \
	-or \
	\( \
		-type d \
		-not -perm 0700 \
		-exec chmod 0700 '{}' ';' \
	\)
```

通过行连接符（反斜杠-回车符序列）和缩进，读者可以清楚地理解这个复杂命令的逻辑。该技术在命令行中也奏效，但是很少用。

为编写脚本而配置 vim

- `:syntax on`用来打开语法高亮，打开这个选项后，在查看`shell`时，不同的`shell`语法元素会以不同的颜色显示
- `:set health`用来将搜索的结果高亮显示。
- `:set tabstop=4`用来设置Tab键造成的空格长度，默认值是 8 列，将这个值设成 4 会让长文本行更容易适应屏幕
- `:set autoindent`用来开启自动缩进特性，这个选项会让 vim 对新的一行缩进程度和上一行保持一致
- 通过把这些命令（不需要冒号字符）添加到`~/.vimrc`文件，则这些改变可以永久生效

## 启动一个项目

该项目的目的是查看如何使用各种`shell`特性来创建程序，更为重要的是，如何创建好的程序

**如何创建变量和常量**

直接`title="aaaa"`，之后`echo $title`即可

对于`shell`来讲，变量和常量没有区别，较为普遍的约定是，我们使用大写字母表示常量，使用小写字母表示变量

**为变量和常量赋值**

变量的值中可以包括什么呢？它包含的是可以扩展为字符串的任意值：

- `a=z`：将字符串`z`的值赋值给`a`
- `b="a string"`：嵌入的空格必须用引号括起来
- `c="astring and $b"`：可以被扩展到赋值语句中的其他扩展，比如变量
- `d=$(ls-l foo.txt)`：命令的结果
- `e=$((5*7))`：算术扩展
- `f="\t\ta string\n"`：转义序列，比如制表符和换行符

可以在一行给多个变量赋值

`a=5 b="a string"`

在扩展期间，变量名称可以用花括号`{}`括起来。比如

`filename="myfile" mv $filename ${filename}1`相当于`mv myfile myfile1`

### here 文档

有一种称为`hure 文档`或是`here 脚本`的方法来输出文本。`here`文档是 I/O 重定向的另外一种形式，我们在脚本中嵌入正文文本，然后将其输入到一个命令的标准输入中，工作方式如下

```shell
command << token
text
token
```

其中，`command`是接受标准输入的文件名，`token`是用来指示嵌入文本结尾的字符串，它和`echo`命令类似，但是它可以在`here`文档中随意嵌入引号，它也可以和接受标准输入的任何命令一起使用

## 自顶向下设计

我们最好将庞大的、复杂的任务，拆分成一些列小的、简单的任务

### shell 函数

`shell`函数有两种语法形式

```shell
# name 为名称
function name {
	# commands 为这个函数中的一系列命令
	commands
	return
}

name () {
	commands
	return
}
```

两种格式等价，并且可以交替使用，下面的脚本演示了`shell`函数的使用

```shell
# shell 函数定义在脚本的位置必须在它被调用的前面
function funct {
	echo "Step 2"
	# return 会将控制权还给函数后调用后面的代码
	return
}

echo "Step 1"
# 当 shell 读取脚本的时候，会跳过前面定义函数的部分，在调用时直接执行
funct
echo "Step 3"
```

### 局部变量

在我们目前编写的脚本中，所有的变量都是全局变量。全局变量在整个程序期间会一直存在。局部变量仅仅在定义它们的`shell`函数中有效，一旦`shell`函数终止，它们就不再存在

```shell
func_1 () {
	# 局部变量通过 local 来定义
	local foo
	foo=1
	echo "func_1: foo = $foo"
}
```

### 保持脚本的运行

在开发程序时，让程序保持可运行的状态非常有用。通过这种方式并经常测试，可以在开发过程的早期检测到错误。比如可以添加空函数，在早期验证函数的运行流程

`shell`函数可以很好地取代别名，如果我们很喜欢为脚本开发`report_disk_space`这个`shell`函数，则可以为我们的`.bashrc`文件创建一个相似到名为`ds`的函数

## 流控制：IF 分支语句

### 使用 if

通过`shell`，我们可以对一些逻辑进行编码

```shell
x=5

if [ $x = 5 ]; then
	echo "x equals 5."
else
	echo "x does not equal 5."
fi
```

`if`语法格式如下

```shell
if commands; then
	# commands 可以是一组命令
	commands
[elseif commands; then commands...]
[else commands]
fi
```

### 退出状态

命令在执行完毕后，会向操作系统发送一个值，称之为”退出状态“。这个值是`0~255`的整数，用来指示命令执行成功还是失败。按照惯例，数值`0`表示成功，其他表示失败。`shell`提供了一个可以用来检测退出状态的参数，也就是`$?`

`ls -d /usr/bin` => `/usr/bin` => `echo $?` => `0`

`ls -d /bin/usr` => `ls: cannot access /bin/usr: No such file or diretory =>  => `echo $?` => `2`

`shell`提供了两个非常简单的内置命令，它们不做任何事情，除了一个`0`或`1`退出状态来终止执行。

`true` => `echo $?` => `0`

`false` => `echo $?` => 1

### 使用 test 命令

目前为止，经常和`if`一起使用的命令是`test`。`test`会执行各种检查和比较。这个命令有两种等价的形式

`test expression`

以及更流行的`[ expression ]`

这里的`expression`是一个表达式，其结果是`true`或`false`

**文件表达式**

具体见 P332

**字符串表达式**

| 表达式            | 成为 true 的条件                  |
| ----------------- | --------------------------------- |
| string            | string 不为空                     |
| -n string         | string 的长度大于 0               |
| -z string         | string 的长度等于 0               |
| string1=string2   | string1 和 string2 相等           |
| string1==string2  | string1 和 string2 相等           |
| string1!==string2 | string1 和 string2 不相等         |
| string1>string2   | 在排序时，string1 在 string2 之后 |
| string1<string2   | 在排序时，string1 在 string2 之前 |

> 在使用 test 命令时候，">" 和 "<" 运算符必须用引号括起来（或是使用反斜杠进行转义）。如果不这样做，会被 shell 解释为重定向操作符

**整数表达式**

| 表达式                | 成为 true 的条件            |
| --------------------- | --------------------------- |
| integer1 -eq integer2 | integer1 和 integer2 相等   |
| integer1 -ne integer2 | integer1 和 integer2 不相等 |
| integer1 -ne integer2 | integer1 小于等于 integer2  |
| integer1 -ne integer2 | integer1 小于 integer2      |
| integer1 -ne integer2 | integer1 大于等于 integer2  |
| integer1 -ne integer2 | integer1 大于 integer2      |

### 更现代的 test 命令版本

bash 的版本最近包含了一个复合命令，它相当于增强的`test`命令，下面是用法

`[ [ expression ] ]`

`expression`是一个表达式，其结果为`true`或`false`，它和`test`命令类似，但是它增加了一个很重要的新字符串表达式

`string1=~regex`

`[ [ ] ]`增加的另外一个特性是`==`操作符支持模式匹配，就像路径名扩展那样

`if [ [ $FILE == foo.* ] ];then`

### (( )) 为整数设计

该命令支持一套完整的算术计算

`(( ))`用于执行算术真值测试`arithmetic truth test`。当算数结果是非零值时，则算术真值测试为`true`

### 组合表达式

我们可以将表达式组合起来创建更复杂的计算。表达式是使用逻辑运算符组合起来的。与`test`和`[[ ]]`命令配套的逻辑运算符有三个，它们是`AND`、`OR`和`NOT`

| Operation | test | [[]] and (()) |
| --------- | ---- | ------------- |
| AND       | -a   | &&            |
| OR        | -o   | \|\|          |
| NOT       | !    | !             |

到目前为止`test`更为传统，而`[[ ]]`是 bash 特定的。

### 控制运算符

bash 还提供了两种可以执行分支的控制运算符。`&&(AND)`和`||(OR)`

`command1 && command2`，只有当`command1`执行成功时，`command2`才会执行

`command1 || command2`，只有当`command1`执行失败时，`command2`才会执行

## 读取键盘输入

尽管很多程序不需要与用户交互，但对一些用户来说，直接从用户获取输入比较好

### read 从标准输入读取输入值

内嵌命令`read`的作用是读取一行标准输入。此命令可用于读取键盘输入值或应用重定向读取文件中的一行。`read`命令的语法结构如下所示

`read [-options] [variable...]`

语法中`options`列出的一条或多条可用的选项，而`variable`则是一到多个用于存放输入值的变量。如果没有提供任何此类变量，则由`shell`变量`REPLY`来存储数据行

- `-a array`：将输入值从索引为 0 的位置开始赋给 array
- `-d delimiter`：用字符串`delimiter`的第一个字符标志输入的结束，而不是新的一行的开始
- `-e`：使用`Readline`处理输入，此命令使用户能够使用命令行模式的相同方式编辑输入
- `-n num`：从输入中读取`num`个字符，而不是一整行
- `-p prompt`：使用`prompt`字符串提示用户进行输入
- `-r`：原始模式。不能将后斜线字符翻译为转义码
- `-s`：保密模式。不在屏幕显示输入的字符。此模式在输入密码和其他机密信息时很有用
- `-t seconds`：超时。在`seconds`秒后结束输入。如果输入超时，`read`命令返回一个非 0 的退出状态
- `-u fd`：从文件说明符`fd`读取输入，而不是从标准输入中读取

```shell
echo -n "please enter an integer."
read int
# 之后通过 $int 获取输入值
```

如果`read`命令之后没有变量，则会为所有输入分配一个`shell`变量`REPLY`

```shell
echo -n "Enter one or more values > "
read
echo "REPLY = '$REPLY'"
```

**选项**

使用不同的选项，`read`命令可以达成不同的有趣的效果，比如使用`-t`和`-s`选项写出读取"秘密"输入的脚本

```shell
if read -t 10 -sp "Enter secret passphrase > " secret_pass; then
	echo -e "\nSecret passphrase = '$secret_pass'"
else
	echo -e "\nInput timed out" >&2
	exit 1
fi
```

上述脚本会提示用户输入秘密通行短语，系统等待时间为 10s，如果 10s 内用户没有完成输入，脚本以出错状态结束运行。又因为使用了`-s`选项，输入的密码不会显示到屏幕上

**使用 IFS 间隔输入字段**

通常`shell`会间隔提供给`read`命令的内容。这也就意味着，在输入行，由一个或多个空格将多个但是分割成为分离的单项，再由`read`命令将这些单项赋值给不同的变量。此行为是由`shell`变量`IFS(Internal Field Separator)`设定的。`IFS`的默认值包含了空格、制表符和换行符，每一种都可以将字符彼此分隔开

我们可以通过改变`IFS`值来控制`read`命令输入的间隔方式，例如，文件`/etc/passwd`的内容使用冒号作为字段之间的分隔符。将`IFS`的值改为单个冒号即可使用`read`命令读取`/etc/passwd`文件的内容，并成功将个字段分隔为不同变量。

通常`read`命令会从标准输入中获取输入，而不能采取以下方式

`echo "foo" | read`

这种方法看似可以运行成功，其实不然。这主要是由于`shell`处理管道的方式。在 bash 中，管道会创造子 shell。子 shell 复制了 shell 以及 pipeline 执行命令过程中使用到的 shell 环境。进程结束后，所复制的 shell 环境即被销毁，意味着 子 shell 永远不会改变父类进程的 shell 环境。read 命令给变量赋值，这些变量会成为 shell 环境的一部分，当命令推出时，子 shell 与其环境被销毁，read 命令的复制效果就被丢失

### 验证输入

通常来讲，程序写得好与不好的差别仅仅在于程序是否能够处理突发意外情况。以下是一个对不同类型进行输入验证的程序

```shell
invalid_input () {
	echo "Invalid inpu '$REPLY'" >&2
	exit 1
}

read -p "Enter a single item >"

# input is Empty (invalid)
[[ -z $REPLY ]] && invalid_input

# input is multiple items (invalid)
(( $(echo $REPLY | wc -w) > 1 )) && invalid_input

# is input a valid filename?
if [[ $REPLY =~ ^[-[:alnum:]\._]+$ ]]; then
	echo "'$REPLY' is a valid filename."
	if [[ -e $REPLY ]]; then
		echo "And file '$REPLY' exists."
	else
		echo "However, file '$REPLY' dose not exist."
	fi
	
	# is input a floating point number?
	if [[ $REPLY = ~ ^-?[[:digit:]]*\.[[:digit:]]+$ ]]; then
		echo "'$REPLY' is a floating point number."
	else
		echo "'$REPLY' is not a floating point number."
	fi
	
	# is input an integer?
	if [[ $REPLY =~ ^-?[[:digit:]]+$ ]]; then
		echo "'$REPLY' is an integer."
	else
		echo "'$REPLY' is not an integer."
	fi
else
	echo "The string '$REPLY' is not a valid filename."
fi
```

### 菜单

菜单驱动是一种常见的交互方式。在菜单驱动的程序会呈现个用户一系列的选项，并请求用户进行选择。

```shell
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
		exit
	fi
	if [[ $REPLY == 1 ]]; then
		echo "Hostname: $HOSTNAME"
		uptime
		exit
	fi
	if [[ $REPLY == 2 ]]; then
		df -h
		exit
	fi
    if [[ $REPLY == 3 ]]; then
    	if [[ $(id -u) -eq 0 ]]; then
    		echo "Home Space Utilization (All Users)"
   			du -sh /home/*
    	else
    		echo "Home Space Utilization ($USER)"
    		du -sh $HOME
    	fi
    	exit
	fi
else
	echo "Invalid entry."
	exit 1
fi
```



