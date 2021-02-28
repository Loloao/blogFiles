# vim
vim 是从`vi`发展而来的一个文本编辑器，这种效率（装逼）神器实在是深得我心，用它来写`markdown`文件既能写的快又能熟悉语法。在拜读完`vimtutor`之后，我马上使用它来写下这篇文章来记录 vim 的那些基本操作。
## 基本原理

### 命令和对象
vim 的许多命令都是都是由一个操作符和一个动作构成，比如使用删除操作的命令：
`d motion`
一个简短的动作列表：
- `w`: `word`从当前光标当前位置到下一个单词的起始处，不包括它的第一个字符。
- `e`: 从当前光标当前位置到当前单词的末尾，包括最后一个字符。
- `b`: `back`从当前光标到上一个单词的起始处
- `$`: 从当前光标直到行末。
text object：
- `s`: 句子
- `w`: 单词
- `p`: 段落
- `iw`: `inner word`当前单词内部
- `it`: `inner tag`HTML 标签内的内容
- `i"`: `inner quotes`quotes 周围的内容，比如`''`，`""`，`[]`
- `ip`: `inner paragraph`段落内
- `as`: `a sentence`句子内
- `ae`: `entire`当前文件所有内容
- `ii`: `indent`相同缩进的内容

操作符：
- `d`: `Delete`删除
- `c`: `Change`更改
- `>`: `Indent`缩进
- `v`: `Visual select`视图选择
- `y`: `Yank`复制
- `f(F)`: `find`查找到匹配项
- `t(T)`: `find`查找到匹配项但不包括匹配项[other]
custom operators，一些插件：
- `Surround`: `ds"`当前所在位置周围的 quotes 比如`''`，`""`，`[]`删除
- `Commentary`: `cml`将当前行注释
- `ReplaceWithRegister`: `griw`将剪切板内容代替当前单词
- `Title case`: `gtip`将`title`首字母大写，用于`markdown`
- `Sort case`: vim 在可视模式下有`sort`操作，但不方便，`gsip`将当前段落排序
- `System copy`: 将剪切板内容显示
### 使用计数指定动作
在动作前输入数字可以使它重复这么多次，比如删除动作：
`d number motion`

### 模式
- `normal`正常模式
- `insert`插入模式
- `command`命令模式，比如`:wq`，`:q!`，`:vs`分屏，`sp(split)`，通过`:q`退出分屏模式
- `visual`可视模式，一般用于块状选择文本，`V`选择行，`ctrl v`块状选择

## 文本操作

### 移动光标
- `h`左移
- `j`下移
- `k`上移
- `l`右移
- `0`移动到行首第一个字符
- `^`移动到第一个非空白字符
- `$`移动到行尾
- `g_`移动到行尾非空白字符
- `gi`快速跳到最后一次编辑的地方并进入插入模式
- `w`移动到下一个单词的开头，此处指的是以为空白符分割的单词
- `W`移动到下一个单词的开头，此处是指以空白符分割的单词
`e E b B`和`w W`相同逻辑
- `f<char>`可以移动到`char`字符上
- `t<char>`可以移动到`char`的前一个字符上，如果搜到，可以使用分号`;`或`,`继续搜索该行上一个，下一个
- `F<char>`搜索前面的字符
- `()`在句子间进行移动
- `{}`在段落间进行移动
- `ctrl o`移动到上一个位置
- `H(head)`跳到当前屏幕开头
- `M(middle)`跳到当前屏幕中间
- `L(lower)`跳到当前屏幕结尾
- `ctrl u`翻到下一页
- `ctrl f`翻到上一页
- `zz`把屏幕置为中间

### 复制动作
- `.`可以重复你的上一次动作

### 删除
`d`开头的删除命令可以将删除的内容放入一个寄存器中，使用`p`命令可以取出
- `x`删除光标所在的当前字符
- `dw` 可以删除当前单词，当输入`d`时，屏幕右下会出现`d`单词，如果单词不对，需要按下`esc`重新再来
- `daw`表示`delete around word`会删除单词以及周围的空格，实际上就是`dw`
- `d$` 删除当前单词到末尾
- `dd` 删除当前行，因为这个命令实在太高频了

### 插入
- `i`可以进入插入模式，按下`esc`键可以退出插入模式

### 添加
- `a`: `append`可以在当前位置后面添加文本内容，需要使用`esc`键退出
- `A`: 可在当前行的最后加入内容
- `I`: 在本行开头插入内容

### 撤销
- `u`撤销最后执行的命令
- `U`撤销对整行的修改
- `Ctrl r`可以重做被撤销的命令，也就是撤销掉撤销命令

### 更改
- `c`相当于`d`命令，只不过是操作是更改
- `cw`以及正确的单词可以更改单词，但它会进入插入模式，记得使用`esc`退出插入模式

### 置入
- `p`可以将最后一次删除的内容置于光标之后

### 替换
- `r(replace)`可以输入一个字符替换当前字符
- `R`可连续替换多个字符
- `s(substitute)`替换一个字符并进入插入模式
- `S`把整行删除并进入插入模式
- `C`删除整行并进入插入模式

### 打开
- `o`可以在当前行下方打开新的一行，同时进入插入模式
- `O`可以在当前行上方打开新的一行，同时进入插入模式

### 复制粘贴
vim 的复制粘贴会在`normal`模式或是`insert`模式下有不同的行为
vim 里操作的是寄存器而不是系统剪切板，`d`或是`y`复制的内容都放到了一个无名寄存器中，vim 使用多组寄存器
- `"<register>`前缀可以指定寄存器，不指定默认使用无名寄存器，比如`"ayy`将当前行复制到`a`寄存器中
- `:reg <register>`可查看寄存器中保存的内容
除了有名寄存器`a - z`，vim 中还有一些其他常见寄存器
- `"0`复制专用寄存器，使用`y`复制文本时会被拷到复制寄存器`0`
- `"+"`复制到系统剪切板
- `"%`当前文件名
- `".`上次插入的文本
- `:set clipboard=unnamed`可以直接复制粘贴系统剪切板内容
`normal`模式
- `y`用于复制文本，可输入`v`进入可视模式之后使用`y`进行复制，之后输入`p`进行粘贴
- `yw`也是操作符可以加入`w`或是`e`之类的进行操作
`insert`模式
- `ctrl c | v`进行复制粘贴

### 插入模式命令
- `ctrl h`删除上一个字符
- `ctrl w`删除上一个单词
- `ctrl u`删除当前行
可以设置`ctrl c`或是`ctrl [`进行正常模式和插入模式的切换

### 补全
- `ctrl n`普通关键字
- 当补全有列表时，使用`ctrl n | ctrl p`选择上一个或是下一个，`n: next`，`p: previous`
- `ctrl x + ctrl f`文件名补全，会展示文件路径
- `ctrl x + ctrl 0`全能补全，补全代码，需要加上插件

## 文件操作

### 定位及文件状态
- `ctrl g`可以显示当前编辑文件中当前光标所在行位置以及文件状态信息
- `G`可以直接跳转到某一指定行，先输入行号再输入大写`G`

### 搜索类命令
- `\`加上一个字符串可以在当前文件中查找该字符串
- `n`可以重复查找搜索的字符串
- `?`加上一个字符串可在当前文件中逆向查找该字符串
- `*`或是`#`进行当前单词的前向或是后向匹配
- `ctrl o`可以回退到之前的位置
- `ctrl i`跳转到较新的位置
- `:set ic`可以设置忽略大小写搜索
- `:set noic`可以设置不忽略大小写搜索
- `:set hls`高亮所有匹配短语

### 配对括号的查找
- 把光标放在任何一个`(`, `{`，或是`[`上，按`%`字符，此时光标会出现在配对的括号处，再按`%`可以跳会第一次按括号的地方

### 替换操作
- `:s/old/new/ 回车`可以替换光标所在行的第一个匹配的`old`为`new`
- `:s/old/new/g`则该行的所有匹配串都被替换
- `:#,#s/old/new/g`其中`#,#`表示替换操作的若干行中首尾两行的行号
- `:%s/old/new/g`替换整个文件的匹配字符串
- `:%s/old/new/gc`对整个文件的每个匹配字符串提示是否进行替换
- `g(global)`全局替换
- `c(confirm)`表示确认，可以确认或替换修改
- `n(number)`报告匹配的次数而不替换，可用来查询匹配次数
- `ctrl r`用于撤销替换

### 在 vim 内执行外部操作
- `:!`输入一行命令，所有以`:`开头输入的命令都需要以`回车`结束才会执行

### 保存文件
- `:q!`不保存文件强行退出
- `:wq`保存文件修改退出
- `:w 文件名`可以将当前文件以文件名保存
- `v`键可以进入可视模式，移动光标对文本进行选取，之后按`:`可以看到屏幕底部出现了`:'<,'>`之后输入`w 文件名`就可以将选中文本保存到另一个文件

### 提取和合并文件
- `:r 文件名`可以将创建的文件提取进来，所提取的文件将在光标处置入

### 多文件操作
多文件操作有几个概念
- `Buffer`指打开一个文件的内存缓冲区，vim 打开文件都是打开缓冲区，修改的都是针对文件中的缓冲区，直到运行`:w`就可将修改内容写入文件
- `Window`指`Buffer`可视化的分割区域
- `Tab`可以组织窗口为一个工作区
`Buffer`命令
- `:ls`列举当前缓冲区
- `:b n`跳转到第 n 个缓冲区
- `:bpre | :bnext | :bfirst | :blast`跳转到对应 buffer
- `:e <文件路径>`可以编辑对应文件
`Window`命令
一个缓冲区可分割为多个窗口，每个窗口可打开不同缓冲区
- `Ctrl w`切换窗口
- `Ctrl w + h`移动到左边窗口
- `Ctrl w + j`移动到下边窗口
- `Ctrl w + k`移动到上边窗口
- `Ctrl w + l`移动到右边窗口
- `Ctrl w + s`水平分割，或`:sp`
- `Ctrl w + v`垂直分割，或`:vs`
每个窗口可以被无限分割
`Tab`是可以容纳一系列窗口的容器

## 其他命令
- `:colorsheme`用于显示当前的主题配色，默认是`default`
- `:colorsheme <ctrl + d>`显示所有配色
- `:colorsheme <配色名>`修改配色
[配色主题下载](https://github.com/flazz/vim-colorshemes)

## 宏(macro)
宏可以看成是一系列操作的集合，可以使用宏录制一系列操作，然后用于回放，宏的使用分为录制和回放
- `q`来进行录制，同时也是`q`结束录制
- `q<register>`选择想要保存的寄存器，把录制的命令保存其中
- `@<register>`回放寄存器中保存的一系列命令
- `:normal`可使用`normal`模式下命令
- `: + ctrl p`可复制上一个命令

## 配置

### 常见配置
- `:h option-list`查找所有可配置项
- `set number`设置行号
- `colorsheme hybird`设置主题
- `"`进行注释
- `:w`保存`.vimrc`之后要通过`:source ~/.vimrc`让配置效

### 映射
vim 映射就是把一个操作映射到另一个操作
- `:map <新操作符> <代替的操作符>`可以临时设置代替的映射
- `:unmap <新操作符>`取消映射
- `let mapleader = ","`用于设置`leader`键，常用的是`,`或是空格，比如`inoremap <leader>w <Esc>:w<cr>`在插入模式保存
vim 的`normal/visual/insert`模式下都可以定义映射
- `nmap | vmap | imap`定义映射只在`normal/visual/insert`模式下分别有效
比如`:imap <c-d> <Esc>ddi`可以在插入模式下使用`ctrl d`删除一行
递归映射就是`a`命令替换为`b`命令，再用`c`命令替换`b`命令，会发现`c`命令允许的是`a`命令的动作。`map`命令就有这个风险
vim 提供了非递归映射，这些命令不会递归解释，比如
`nnoremap/vnoremap/inoremap`其中`nore`表示`no recursive`非递归
- *innoremap jj <Esc>`^*其中的*`^*表示的是回到插入模式编辑的地方
- `cnoremap w!! w !sudo tee % >/dev/null`可以执行外部命令，此处是使用`w!!`进行使用`sudo`写入

### 开源配置
- [SpaceVim](https://github.com/SpaceVim/SpaceVim)
- [vim-config](https://github.com/PegasusWang/vim-config)

### 在线帮助
- 按下`HELP`键
- 按下`F1`键
- 输入`:help 回车`键
- 提供一个正确的参数给`help`命令，可以找到该主题的在线帮助

### 创建启动脚本
- `:edit ~/.vimrc`用于编辑 vim 配置文件

### 补全功能
- `ctrl D`会显示匹配的命令列表，之后按`tab`键可补全命令，它对于`:help`命令非常有效

## 插件
Vim 插件是使用`vimscript`或是其他语言编写的 vim 功能扩展。
在原来管理插件就是复制插件的代码，现在 vim 有很多插件管理器

### vim-plug
- 首先需要[下载](https://github.com/junegunn/vim-plug)
- `Plug(<插件名>)`在`~/.vimrc`中增加该插件名称
- 重新启动 vim 或是`:source ~/.vimrc`
- `:PlugInstall`安装插件

### 一些插件
搜寻插件可以去[vimawesome](https://vimawesome.com/)
- [vim-startify](https://github.com/mhinz/vim-startisfy)配置开屏画面
- [vim-airline](https://github.com/vim-airline/vim-airline)美化状态栏
- [indentline](https://github.com/yggdroot/indent-line)增加代码缩进线
- [vim-hybird](https://github.com/w0ng/vim-hybird)配色
- [solarized](https://github.com/altercation/vim-colors-solarized)配色
- [gruvbox](https://github.com/morhetz/gruvbox)配色
- [nerdtree](https://github.com/scrooloose/nerdtree)文件管理
- [ctrlp](https://github.com/ctrlpvim/ctrlp.vim)用于快速查找并打开一个文件
- [easymotion](https://github.com/easymotion/vim-easymotion)文件内快速移动插件
- [vimsurround](https://github.com/tope/vim-surround)编辑包围的符号
- [fzf](https://github.com/junegunn/fzf)模糊搜索
- [deoplete.nvim](https://github.com/shougo/deoplete.nvim)异步补全代码插件，支持模糊匹配，需要安装对应组件的扩展
- [coc.nvim](https://github.com/neoclide/coc.nvim)异步补全插件，支持 LSP
- [Neoformat](https://github.com/sbdchd/neoformat)代码异步格式化，但需要安装对应语言的库，比如 js 的 prettier
- [ale](https://github.com/w0rp/ale)lint 插件，需要对应的 eslnt 库，比如 eslint
- [vim-commentary](https://github.com/tpope/vim-commentary)注释代码
- [fugitive](https://github.com/tpope/vim-fugitive)在 vim 中使用 git
- [vim-gitgutter](https://github.com/airblade/vim-gitgutter)修改文件之后显示文件的变动
- [tig](https://github.com/junegunn/gv.vim)在命令行查看提交记录


## tips

### . 操作
- 使用泛用性更强的命令，比如使用`iw`代替`w`即使是在句子开头
- 尽可能地使用文本对象代替动作
- 使用重复插件`Repeat.vim`


